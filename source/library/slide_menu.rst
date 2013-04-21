## 概述

手机软件上最近侧滑菜单很流行。。Android平台上也有很多开源的库可以构造侧滑的菜单，其中，这个Facebook like slideout navigation并不是功能最多，也不是最漂亮的，但是我目前找到的实现了侧滑菜单，代码最少的。。适合新手学习背后的原理实现。

## 目录结构

lib目录

	~/source/android/lib/android-fb-like-slideout-navigation/lib(master ✔) tree
	.
	├── AndroidManifest.xml
	├── proguard.cfg
	├── project.properties
	├── res
	│   ├── drawable-hdpi
	│   │   └── ic_launcher.png
	│   ├── drawable-ldpi
	│   │   └── ic_launcher.png
	│   ├── drawable-mdpi
	│   │   └── ic_launcher.png
	│   ├── layout
	│   │   └── slideout.xml
	│   └── values
	│       └── strings.xml
	└── src
	    └── com
	        └── korovyansk
	            └── android
	                └── slideout
	                    ├── SlideoutActivity.java
	                    ├── SlideoutHelper.java
	                    └── utils
	                        └── ScreenShot.java
	                        
	                      
Sample目录

	~/source/android/lib/android-fb-like-slideout-navigation/lib(master ✔) tree ../sample 
	../sample
	├── AndroidManifest.xml
	├── libs
	│   └── android-support-v4.jar
	├── proguard.cfg
	├── project.properties
	├── res
	│   ├── drawable-hdpi
	│   │   ├── bg_android.png
	│   │   ├── bg_android_reverse.png
	│   │   └── ic_launcher.png
	│   ├── drawable-ldpi
	│   │   └── ic_launcher.png
	│   ├── drawable-mdpi
	│   │   └── ic_launcher.png
	│   ├── drawable-xhdpi
	│   │   └── ic_launcher.png
	│   ├── layout
	│   │   ├── sample.xml
	│   │   └── sample_actionbar.xml
	│   ├── values
	│   │   ├── strings.xml
	│   │   └── styles.xml
	│   └── values-v11
	│       └── styles.xml
	└── src
	    └── com
	        └── korovyansk
	            └── android
	                └── sample
	                    └── slideout
	                        ├── MenuActivity.java
	                        ├── MenuFragment.java
	                        ├── SampleActionbarActivity.java
	                        └── SampleActivity.java
	                     

## 使用解析

在Sample这个实例项目中，AndroidMinifest指定最低版本号是7，适用于几乎左右主流机型。libs目录下用了android.support.v4这个包。

项目很简单，3个Activity，一个Fragment。

先来看看最简单的SampleActivity


	
	
	package com.korovyansk.android.sample.slideout;
	

	
	public class SampleActivity extends Activity {
	
		@TargetApi(11)
		@Override
		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.sample);
			if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB){
		    	getActionBar().hide();
		    }
			findViewById(R.id.sample_button).setOnClickListener(
					new View.OnClickListener() {
						@Override
						public void onClick(View v) {
							int width = (int)TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 40, getResources().getDisplayMetrics());
							SlideoutActivity.prepare(SampleActivity.this, R.id.inner_content, width);
							startActivity(new Intent(SampleActivity.this,
									MenuActivity.class));
							overridePendingTransition(0, 0);
						}
					});
		}
	
	
	}
	
额，如果版本是蜂巢及以上系统，就隐藏系统自带默认的ActionBar。。因为布局文件里面定义了一个类似于ActionBar的比较简陋的带按钮的标题栏。。


然后就是对那个按钮点击的监听

先用

	int width = (int)TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 40, getResources().getDisplayMetrics());

动态得到了一个宽度。。。具体是什么宽度呢。。有待研究。。。

然后                        

	SlideoutActivity.prepare(SampleActivity.this, R.id.inner_content, width);
	startActivity(new Intent(SampleActivity.this,
                                MenuActivity.class));
                        overridePendingTransition(0, 0);
	
调用了SlideoutActivity的一个静态方法prepare，然后启动了MenuActivity，并且重载了动画效果。



下面看看MenuActivity，其实是一个FragmentActivity。



	package com.korovyansk.android.sample.slideout;
	
	import android.os.Bundle;
	import android.support.v4.app.FragmentActivity;
	import android.view.KeyEvent;
	
	import com.korovyansk.android.slideout.SlideoutHelper;
	
	public class MenuActivity extends FragmentActivity{
	
		@Override
		public void onCreate(Bundle savedInstanceState) {
		    super.onCreate(savedInstanceState);
		    mSlideoutHelper = new SlideoutHelper(this);
		    mSlideoutHelper.activate();
		    getSupportFragmentManager().beginTransaction().add(com.korovyansk.android.slideout.R.id.slideout_placeholder, new MenuFragment(), "menu").commit();
		    mSlideoutHelper.open();
		}
	
		
		@Override
		public boolean onKeyDown(int keyCode, KeyEvent event) {
			if(keyCode == KeyEvent.KEYCODE_BACK){
				mSlideoutHelper.close();
				return true;
			}
			return super.onKeyDown(keyCode, event);
		}
	 
	
		public SlideoutHelper getSlideoutHelper(){
			return mSlideoutHelper;
		}
		
		private SlideoutHelper mSlideoutHelper;
	
	}
	
	

主要就是定义了一个SlideoutHelper，以及对其的使用

 	
	 	  private SlideoutHelper mSlideoutHelper;
	 	  
在onCreate方法中：

	        mSlideoutHelper = new SlideoutHelper(this);
	        mSlideoutHelper.activate();
	        getSupportFragmentManager().beginTransaction().add(com.korovyansk.android.slideout.R.id.slideout_placeholder, new MenuFragment(), "menu").commit();
	        mSlideoutHelper.open();
	     
然后在冲载了onKeyDown方法，如果按下返回键，调用这个SlideoutHelper的close方法。


MenuFragment继承了ListFragment

	
	package com.korovyansk.android.sample.slideout;
	
	
	public class MenuFragment extends ListFragment {
	
		@Override
		public void onActivityCreated(Bundle savedInstanceState) {
			super.onActivityCreated(savedInstanceState);
			setListAdapter(new ArrayAdapter<String>(getActivity(),
					android.R.layout.simple_list_item_1, new String[] { " First", " Second", " Third", " Fourth", " Fifth", " Sixth"}));
			getListView().setCacheColorHint(0);
		}
	
		@Override
		public void onListItemClick(ListView l, View v, int position, long id) {
			super.onListItemClick(l, v, position, id);
			((MenuActivity)getActivity()).getSlideoutHelper().close();
		}
	
		
	}

		
效果就是那个滑动菜单的列表

SampleActionbarActivity和SampleActivity一样，只是用了系统自己的ActionBar。


	package com.korovyansk.android.sample.slideout;
	
	import android.app.Activity;
	import android.content.Intent;
	import android.os.Build;
	import android.os.Bundle;
	import android.util.TypedValue;
	import android.view.MenuItem;
	
	import com.korovyansk.android.slideout.SlideoutActivity;
	
	public class SampleActionbarActivity extends Activity {
	
		@Override
		public void onCreate(Bundle savedInstanceState) {
		    super.onCreate(savedInstanceState);
		    if(Build.VERSION.SDK_INT < Build.VERSION_CODES.HONEYCOMB){
		    	finish();
		    }
		    setContentView(R.layout.sample_actionbar);
		    
		}
		
		@Override
		public boolean onOptionsItemSelected(MenuItem item) {
			if(item.getItemId() == android.R.id.home){
				int width = (int)TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 40, getResources().getDisplayMetrics());
				SlideoutActivity.prepare(SampleActionbarActivity.this, R.id.inner_content, width);
				startActivity(new Intent(SampleActionbarActivity.this, MenuActivity.class));
				overridePendingTransition(0, 0);
			}
			return true;
		}
		
	}
	
	
## Lib解析

Lib目录下的内容是如此之少。。。我觉得完全可以省略掉Lib。。把两个核心文件放到项目文件中。。。

首先看无关滑动菜单，但是很有意思的ScreenShot类：
	
	
	package com.korovyansk.android.slideout.utils;
	
	import android.app.Activity;
	import android.graphics.Bitmap;
	import android.graphics.Bitmap.Config;
	import android.graphics.Canvas;
	import android.view.View;
	
	public class ScreenShot {
		private final View view;
	
		/** Create snapshots based on the view and its children. */
		public ScreenShot(View root) {
			this.view = root;
		}
	
		/** Create snapshot handler that captures the root of the whole activity. */
		public ScreenShot(Activity activity) {
			final View contentView = activity.findViewById(android.R.id.content);
			this.view = contentView.getRootView();
		}
	
		/** Create snapshot handler that captures the view with target id of the activity. */
		public ScreenShot(Activity activity, int id) {
			this.view = activity.findViewById(id);
		}
		
		/** Take a snapshot of the view. */
		public Bitmap snap() {
			Bitmap bitmap = Bitmap.createBitmap(this.view.getWidth(),
					this.view.getHeight(), Config.ARGB_8888);
			Canvas canvas = new Canvas(bitmap);
			view.draw(canvas);
			return bitmap;
		}
	}


可以生成View，Activity的快照～返回的是Bitmap类型。

在看SlideoutActivity 

	package com.korovyansk.android.slideout;
	
	import android.app.Activity;
	import android.os.Bundle;
	import android.view.KeyEvent;
	
	public class SlideoutActivity extends Activity {
	
		public static void prepare(Activity activity, int id, int width){
			SlideoutHelper.prepare(activity, id, width);
		}
		
		@Override
		public void onCreate(Bundle savedInstanceState) {
		    super.onCreate(savedInstanceState);
		    mSlideoutHelper.activate();
		    mSlideoutHelper.open();
		}
	
		@Override
		public boolean onKeyDown(int keyCode, KeyEvent event) {
			if(keyCode == KeyEvent.KEYCODE_BACK){
				mSlideoutHelper.close();
				return true;
			}
			return super.onKeyDown(keyCode, event);
		}
		
		private SlideoutHelper mSlideoutHelper;
	}
	
我个人感觉是，这个类是用来给其他类集成用的。。和SampleActivity很相似。。。但因为代码不多。。。完全可以把它融合到项目Activity里面。

实际上，Sample项目用到的也就

	public static void prepare(Activity activity, int id, int width){
			SlideoutHelper.prepare(activity, id, width);
	}

一个静态方法。

再看看SlideoutHelper项目，是整个实现的核心：

	package com.korovyansk.android.slideout;
	
	import android.app.Activity;
	import android.content.Context;
	import android.graphics.Bitmap;
	import android.graphics.Rect;
	import android.view.View;
	import android.view.View.OnClickListener;
	import android.view.ViewGroup;
	import android.view.Window;
	import android.view.WindowManager;
	import android.view.animation.Animation;
	import android.view.animation.Animation.AnimationListener;
	import android.view.animation.TranslateAnimation;
	import android.widget.ImageView;
	import android.widget.RelativeLayout.LayoutParams;
	
	public class SlideoutHelper {
	
		private static Bitmap sCoverBitmap = null;
		private static int sWidth = -1;
	
		public static void prepare(Activity activity, int id, int width) {
			if (sCoverBitmap != null) {
				sCoverBitmap.recycle();
			}
			Rect rectgle = new Rect();
			Window window = activity.getWindow();
			window.getDecorView().getWindowVisibleDisplayFrame(rectgle);
			int statusBarHeight = rectgle.top;
	
			ViewGroup v1 = (ViewGroup) activity.findViewById(id).getRootView();
			v1.setDrawingCacheEnabled(true);
			Bitmap source = Bitmap.createBitmap(v1.getDrawingCache());
			v1.setDrawingCacheEnabled(false);
			if (statusBarHeight != 0) {
				sCoverBitmap = Bitmap.createBitmap(source, 0, statusBarHeight, source.getWidth(), source.getHeight() - statusBarHeight);
				source.recycle();
			} else {
				sCoverBitmap = source;
			}
			sWidth = width;
		}
	
		public SlideoutHelper(Activity activity) {
			this(activity, false);
		}
		
		public SlideoutHelper(Activity activity, boolean reverse) {
			mActivity = activity;
			mReverse = reverse;
		}
	
		public void activate() {
			mActivity.setContentView(R.layout.slideout);
			mCover = (ImageView) mActivity.findViewById(R.id.slidedout_cover);
			mCover.setImageBitmap(sCoverBitmap);
			mCover.setOnClickListener(new OnClickListener() {
				@Override
				public void onClick(View v) {
					close();
				}
			});
			int x = (int) (sWidth * 1.2f);
			if (mReverse) {
				@SuppressWarnings("deprecation")
				final android.widget.AbsoluteLayout.LayoutParams lp = new android.widget.AbsoluteLayout.LayoutParams(LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT, x, 0);
				mActivity.findViewById(R.id.slideout_placeholder).setLayoutParams(lp);
			} else{
				@SuppressWarnings("deprecation")
				final android.widget.AbsoluteLayout.LayoutParams lp = new android.widget.AbsoluteLayout.LayoutParams(LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT, 0, 0);
				mActivity.findViewById(R.id.slideout_placeholder).setLayoutParams(lp);
			}
			initAnimations();
		}
	
		public void open() {
			mCover.startAnimation(mStartAnimation);
		}
	
		public void close() {
			mCover.startAnimation(mStopAnimation);
		}
	
		protected void initAnimations() {
			int displayWidth = ((WindowManager) mActivity.getSystemService(Context.WINDOW_SERVICE)).getDefaultDisplay().getWidth();
			final int shift = (mReverse ? -1 : 1) * (sWidth - displayWidth);
			mStartAnimation = new TranslateAnimation(
					TranslateAnimation.ABSOLUTE, 0,
					TranslateAnimation.ABSOLUTE, -shift,
					TranslateAnimation.ABSOLUTE, 0,
					TranslateAnimation.ABSOLUTE, 0
					);
	
			mStopAnimation = new TranslateAnimation(
					TranslateAnimation.ABSOLUTE, 0,
					TranslateAnimation.ABSOLUTE, shift,
					TranslateAnimation.ABSOLUTE, 0,
					TranslateAnimation.ABSOLUTE, 0
					);
			mStartAnimation.setDuration(DURATION_MS);
			mStartAnimation.setFillAfter(true);
			mStartAnimation.setAnimationListener(new AnimationListener() {
	
				@Override
				public void onAnimationStart(Animation animation) {
				}
	
				@Override
				public void onAnimationRepeat(Animation animation) {
				}
	
				@Override
				public void onAnimationEnd(Animation animation) {
					mCover.setAnimation(null);
					@SuppressWarnings("deprecation")
					final android.widget.AbsoluteLayout.LayoutParams lp = new android.widget.AbsoluteLayout.LayoutParams(LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT, -shift, 0);
					mCover.setLayoutParams(lp);
				}
			});
	
			mStopAnimation.setDuration(DURATION_MS);
			mStopAnimation.setFillAfter(true);
			mStopAnimation.setAnimationListener(new AnimationListener() {
	
				@Override
				public void onAnimationStart(Animation animation) {
				}
	
				@Override
				public void onAnimationRepeat(Animation animation) {
				}
	
				@Override
				public void onAnimationEnd(Animation animation) {
					mActivity.finish();
					mActivity.overridePendingTransition(0, 0);
				}
			});
		}
	
		private static final int DURATION_MS = 400;
		private ImageView mCover;
		private Activity mActivity;
		private boolean mReverse = false;
		private Animation mStartAnimation;
		private Animation mStopAnimation;
	}






人人API集成
====



人人的API使用Oauth2协议。有一些以及封装好的SDK供开发者使用。Android的SDK文档是太监文档，看到下面。。。就发现下面没有了。。。


SDK官网页面：<http://wiki.dev.renren.com/wiki/Android-sdk-3.0>

SDK源代码托管在Bitucket上 <https://bitbucket.org/renren_platform/renren-api-android-sdk/>, 使用Mercurial进行版本控制。

##使用方法##

###在官方网站中注册应用，得到API_KEY等认证字符串。

- 应用ID
- API Key
- Secret Key

###下载SDK，并在自己的项目中加入编译依赖###

注意: AndroidManifest中需要加入INTERNET权限：	
	
	<uses-permission android:name="android.permission.INTERNET" />
	
如果需要在自己的项目中加入一键发布状态，一键上传图片，一键创建相册的功能，则需要注册对应Activity

			
		<!-- 发布状态的Activity，如需使用一键发布状态的功能，则必须在AndroidManifest.xml中声明 -->
		<activity android:name="com.renren.api.connect.android.status.StatusPubActivity"></activity>
		
		<!-- 发布照片的Activity，如需使用一键上传图片的功能，则必须在AndroidManifest.xml中声明 -->
		<activity android:name="com.renren.api.connect.android.photos.UploadPhotoActivity"></activity>
		
		<!-- 创建相册的Activity，如需使用一键创建相册的功能，则必须在AndroidManifest.xml中声明 -->
		<activity android:name="com.renren.api.connect.android.photos.CreateAlbumActivity"></activity>
		
###测试SDK_DEMO

可以下载SDK_DEMO，把API_KEY换成自己的，就可以用自己的账号登陆人人，进行发布状态的测试。

注意，应用没有通过审核前，只能允许应用创建者的账号，或者创建者在管理页手动添加的用户登陆

###SDK_DEMO分析

#### 初始化Renren类

应用的进入点是一个RenrenSDKDemo的Activity。首先注册一个Renren类的实例。其中API_KEY等均为上文提到的创建应用得到的值，为字符串类型。

        Renren renren;
			
        renren = new Renren(API_KEY, SECRET_KEY, APP_ID, this);
        
其中Renren类实现了Parcelable接口，所以可以序列化，用Bundle进行传递。


### 建立listener处理认证后的情况

SDK_DEMO里面是没有经过进入ApiListActivity, 同时传入了带有renren对象的Bundle。

	final RenrenAuthListener listener = new RenrenAuthListener() {

			@Override
			public void onComplete(Bundle values) {
				Log.d("test",values.toString());
				startApiList();
			}

			@Override
			public void onRenrenAuthError(
					RenrenAuthError renrenAuthError) {
				handler.post(new Runnable() {
					
					@Override
					public void run() {
						Toast.makeText(RenrenSDKDemo.this, 
								RenrenSDKDemo.this.getString(R.string.auth_failed), 
								Toast.LENGTH_SHORT).show();
					}
				});
			}

			@Override
			public void onCancelLogin() {
			}

			@Override
			public void onCancelAuth(Bundle values) {
			}
			
		};
		
	
	   private void startApiList() {
			Intent intent = new Intent(this, ApiListActivity.class);
    		intent.putExtra(Renren.RENREN_LABEL, renren);
    		startActivity(intent);
    	}



三种不同的方式进行认证用户信息。

    	
    	oAuthButton = (Button)mainLayout.findViewById(R.id.auth_site_mode);
    	oAuthButton.setOnClickListener(new View.OnClickListener() {
			
			@Override
			public void onClick(View v) {
				renren.authorize(RenrenSDKDemo.this, listener);
			}
		});
    	
    	ssoButton = (Button)mainLayout.findViewById(R.id.sso_mode);
    	ssoButton.setOnClickListener(new View.OnClickListener() {
			
			@Override
			public void onClick(View v) {
				//use default permissions here, see Renren class for detail
				renren.authorize(RenrenSDKDemo.this, null, listener, 1);
			}
		});
    	
    	pwdFlowButton = (Button)mainLayout.findViewById(R.id.password_flow_mode);
    	pwdFlowButton.setOnClickListener(new View.OnClickListener() {
			
			@Override
			public void onClick(View v) {
				PasswordFlowRequestParam param = new PasswordFlowRequestParam(getString(R.string.username), 
																				getString(R.string.password));
				renren.authorize(RenrenSDKDemo.this, param, listener);
			}
		});
		
		

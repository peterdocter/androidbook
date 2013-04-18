使用AndroidAnotation方便的进行控件和监听器绑定
======





* Github Star : 1000+
* 推荐指数：★★★ 
* 评价: 
* Github : https://github.com/excilys/androidannotations

每次写Android，都要写一堆如

	button = (Button)findViewById(R.id.button), button.setOnclickListener(){
		  new View.OnClickListener(){
	            @Override
	            public void onClick(View view) {
	            }
	        }
	}

这样的方法，真烦。。。

而且还有异步操作，使用Thread，Handler。。。。

自从有了AndroidAnotation，一切方便多啦！

使用Java的注解以及反射极致，简化代码！

看看它的一段示例就知道多方便啦！

	@EActivity(R.layout.translate) // Sets content view to R.layout.translate
	public class TranslateActivity extends Activity {

	    @ViewById // Injects R.id.textInput
	    EditText textInput;

	    @ViewById(R.id.myTextView) // Injects R.id.myTextView
	    TextView result;

	    @AnimationRes // Injects android.R.anim.fade_in
	    Animation fadeIn;

	    @Click // When R.id.doTranslate button is clicked 
	    void doTranslate() {
	         translateInBackground(textInput.getText().toString());
	    }

	    @Background // Executed in a background thread
	    void translateInBackground(String textToTranslate) {
	         String translatedText = callGoogleTranslate(textToTranslate);
	         showResult(translatedText);
	    }
	   
	    @UiThread // Executed in the ui thread
	    void showResult(String translatedText) {
	         result.setText(translatedText);
	         result.startAnimation(fadeIn);
	    }

	    // [...]
	}
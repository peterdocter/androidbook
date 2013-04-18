使用Async-http进行异步http操作
==========


* Github Star : 1200+
* 推荐指数：★★★ 
* 评价: 
* Github : https://github.com/loopj/android-async-http



方便的进行HTTP请求异步回调功能，比起Android默认的采用Java标准库或者Apache的库的HTTP操作+手动异步封装，真是方便多啦！

		AsyncHttpClient client = new AsyncHttpClient();
		client.get("http://www.google.com", new AsyncHttpResponseHandler() {
		    @Override
		    public void onSuccess(String response) {
		        System.out.println(response);
		    }
		});



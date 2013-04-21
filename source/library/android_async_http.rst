Android-async-http用于异步发送HTTP请求，使HTTP交互变得如此简单：

		    AsyncHttpClient client = new AsyncHttpClient();
	
	        client.get("http://www.google.com", new AsyncHttpResponseHandler() {
	            @Override
	            public void onSuccess(String response) {
	                System.out.println(response);
	            }
	        });
	        
	        
因为文档非常齐全。。。使用非常简单。。先不仔细写了。。
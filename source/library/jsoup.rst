

我们写的App，在没有自己的服务器端的情况下，往往要去别人的网站抓取内容。

抓取网站内容有很多办法，其中一个办法是，我们可以下载这个网页的HTML，从里面抽出自己想要的内容。

	<html>
	    <body>
	        <div class="Hello I Am Class">
	             <div id="IAMID">
	             		<p>
	             			I am content
	             		</p>
	             </div>
	        </div>
	    </body>
	</html>

如何从这样的内容里面取到I am content这个东西呢，Jsoup提供了一个简单的方法。用它，就可以像用JQuery的Css选择器一样抽取内容。



使用Jsoup，只要把那个官网下载的Jar包拖进Android项目的libs目录（Eclipese环境下），或者在编译路径中加入这个Jar包。

参考资料

- Jsoup官网 <http://jsoup.org/>
- <http://www.oschina.net/question/12_14127>
- 我之前写过的一个Android程序，同时可以学习异步进程与图片下载 <https://github.com/oa414/McDonalds-Coupon>


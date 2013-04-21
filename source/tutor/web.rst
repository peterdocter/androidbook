#Corona, Phonegap, Titanium的比较
我是一个Web开发者，我想要把我的Web产品移植到iPhone里面，其中一个我的产品就像谷歌地图一样：在手机屏幕哈桑显示地图，你可以拖拽或者缩放地图，并且可以看我们加入到地图的信息。

我知道有一些技术可以让我们用HTML，CSS，Javascript开发本地iPhone程序，我知道一些：

Ansca Mobile（Corona）
PhoneGap
Appcelerator （Titanium）

它们是类似的么，有什么区别么？我应该选择什么？





我注册Stackoverflow就是为了评论那些最高得票的答案。。可是StackOverflow不允许新用户评论。。我只好让我的评论像个答案一样发布。


Rory Blyth的答案是写出了两个Javascript框架的特点，不过，他的核心观点是错的。事实上Titanium和PhoneGap更偏向于类似，它们都把移动平台的一些函数接口暴露给Javascipt API，并且App的核心逻辑运行在Webkit的html,css,Javascipt之上。

1.PhoneGap 不仅是对WebApp的本地包装。通过PhoneGap的Javascirpt API， WebApp可以访问手机的功能，如位置，传感器，相机，联系人，数据库，文件系统，等等。基本上所有的手机SDK的功能都可以桥接到Javascript的世界。另一方面，一个普通的WebApp可以运行在手机浏览器里，但是不能访问大部分的功能）安全性是主要原因。因此，PhoneGap更像是一个移动App而不是一个WebApp。你可在以不使用任何PhoneGapde API的情况下使用PhoneGap包装一个Web ap，但是这不是PhoneGap创造的本意。

2.Titanium 不会编译你的HTML，Css或者Javascript到本地的二进制数据。它把所有资源打包到一个可执行的束，很像嵌入式的图形文件。当一个App运行的时候，这些资源被加载到UIWebView控制器里面，并且运行（作为Javascript，当然不是本地二进制数据）。根本没有一个Javascript-本地代码（或者到Objective-C代码）的编译器。这就像PhoneGap做的一样，从技术架构的观点看，这两个框架是很像的

太长了。。没翻译完
<http://stackoverflow.com/questions/1482586/comparison-between-corona-phonegap-titanium>

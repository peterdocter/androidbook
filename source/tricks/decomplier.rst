#Android反编译与反编译防范

## 反编译Java代码

参考<http://www.oschina.net/question/54100_33457>

文中给出下载链接版本较老，其中dex2jar可能会出现java.lang.OutOfMemoryError的异常。请去官网下载两个工具的最新版。

- <https://code.google.com/p/dex2jar/>
- <http://java.decompiler.free.fr/?q=jdgui>



简单来说，用dex2jar把apk文件解压得到的classes.dex转化为jar文件，然后用JD-GUI打开这个Jar文件，查看源码。

## 反编译apk生成程序的源代码和图片、XML配置、语言资源等文件


同样参考<http://www.oschina.net/question/54100_33457>

使用apktool <https://code.google.com/p/android-apktool/>


## 混淆代码防范反编译

参考 <http://blog.csdn.net/sunboy_2050/article/details/6727640>

修改Android项目下default.properties文件，加上一句

	proguard.config=proguard.cfg

当然同时目录下要有proguard.cfg文件，可以在android_sdk_path/tools/proguard/目录下找

其实似乎现在Android默认创建工程的时候就已经有了。。看一下default.properties注释就可以了。。。

*注意* 

参考<http://my.oschina.net/banxi/blog/55622>

- 当使用了除了android-support-v4这些API的时候，要添加相对应的声明
- 可以让proguard帮我们忽略Log.d()这些语句

	

## TODO 

翻一下Google和StackOverFlow的结果

- [Google搜索Android  decomplier](http://www.google.co.uk/search?hl=zh-CN&newwindow=1&site=&source=hp&q=Android++decomplier&btnG=Google+%E6%90%9C%E7%B4%A2)
- [StackOverFlow decompiling DEX into Java sourcecode](http://stackoverflow.com/questions/1249973/decompiling-dex-into-java-sourcecode)


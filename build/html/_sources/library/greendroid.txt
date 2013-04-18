Android UI库 greendroid
======

.. image:: https://a248.e.akamai.net/camo.github.com/3e7eb43ec0f84e34784622436cb086aabded7707/687474703a2f2f6c68362e67677068742e636f6d2f5f4f484f34793859635162732f545146515466617a4d32492f41414141414141414d70512f6c675070494b496d645a772f733238382f6764352e706e67


* Github Star : 1600+
* 推荐指数：★★★ 
* 评价: 效果还好，但是目前没有持续更新。
* Github : https://github.com/cyrilmottier/GreenDroid 
* 作者的博客文章 : http://cyrilmottier.com/2010/05/14/introduction-to-the-greendroid-library/


一个比较有名的UI库，提供了一些比较好用漂亮的UI控件。

不过根据Github的Commit记录 https://github.com/cyrilmottier/GreenDroid/commits/master ，上一次代码核心功能更新是2011年10月份的事情了。

另外作者是从API level4开始开发这个库的，当时Android SDK还没有ActionBar这个东西。。。所以作者自定义的ActionBar会与SDK的ActionBar控件命名冲突，需要手动解决。。解决方法是，把所有getActionBar方法重构为getGDActionBar（这个名字可以自己起）即可

另外我在处理Google Map API的时候也出现了一些小问题。。

我个人建议是，学习里面的源码，使用或改造一些适合自己实际使用的类。OSChina的Android客户端就是这样干的。。（参见 https://github.com/oschina/android-app 源码）
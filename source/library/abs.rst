使用ActionBarSherlock为低版本的Android系统提供ActionBar
========

.. image::https://a248.e.akamai.net/camo.github.com/b21aae46566b05d5adbb1fa71b88708bc659e120/687474703a2f2f616374696f6e626172736865726c6f636b2e636f6d2f7374617469632f666561747572652e706e67

* Github Star : 3300+
* 推荐指数：★★★★★ 
* 评价: Github上Android项目Star最多的项目！用起来超级方便！快快使用吧！
* Github : https://github.com/JakeWharton/ActionBarSherlock
* 官网：https://github.com/JakeWharton/ActionBarSherlock


Android 从 3.0版本引入ActionBar的概念，但是4.0版本才完善。ActionBarSherlock为你提供一个一致的接口, 让2.1开始的版本都能用上ActionBar。

使用起来非常方便，Activity继承自SherlockActivity, 然后也难怪getSupportXXX代替Android原来的API就可以了。比如，原来用getActionBar的，现在就用getSupportActionBar。


<http://actionbarsherlock.com/>

ActionBarSherlock是Android Support Library的扩展，用一样的API，在Android所有版本中实现ActionBar的效果（ActionBar从4.0版本引入Android，之前的版本要实现ActionBar，必须另外手动定义ActionBar界面）

使用方法：

在Activity中继承Sherlock开头的类，如SherlockActivity,SherlockFragmentActivity。调用 getSupportActionBar代替getActionBar()。





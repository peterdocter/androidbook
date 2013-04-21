StartupNews是丁香园CTO，原淘宝DBA冯大辉用著名的IT业内资讯网站Hackernews的开源系统搭建的面向国内的IT资讯网站，上线不久之后就有人为其做了Android客户端，代码在Github上。（Logo很难看！！！很反胃！！！我都是用自己编译的换掉Logo的版本的！！！）

### 编译依赖

- Jsoup HTML解析库
- ActionBarSherlock 为低版本的系统提供ActionBar
- ViewPageIndicator 分页指示器
- GoogleAnalytics 谷歌用户追踪分析服务
- pulltorefresh  下拉更新

### Application

onCreate里有`EasyTracker.getInstance().setContext(this);`，一句话配置好GoogleAnalytics，太方便啦。

xml文件里面配置的theme是`@style/Theme.Sherlock.Light.DarkActionBar"`

### Utils package

- 把配置信息读取写在一个PreferenceUtils里面，方便读取
- DateUtils提供一个日期格式化为易读格式的函数
- CrashHandler 这个很有意思。实现了UncaughtExceptionHandler接口，当程序crash的时候搜集异常信息写入到文件里面。


### Fragment
- CommentsListFragment， 一个抽象类，定义了pulltorefresh库的接口逻辑。
- CommentsListFragment 评论列表，继承CommentsListFragment，有一个继承自AsyncTask的内部类调用jsoup拉取内容，然后有一个继承BaseAdapter的Adapter渲染listview。
- NewsListFragment类似CommentsListFragment
### Activity 
- AboutActivity 其实是一个PreferenceActivity。实现了一下OnPreferenceChangeListener接口，当设置改变的时候会监听到并刷新本页面显示的内容。同时把ActionBar的icon设置为返回按钮。
- BaseFragmentActivity设置好了GoogleAnalytics，给其他类继承用。
- BrowseActivity从intent里面提取url，用webview加载网页，webview用的WebChromeClient经过自定义，重载onProgressChanged方法，可以配合布局里面一个progressbar更新网页加载进度。
- DiscussActivity抓取了评论，用列表展示
- MainActivity里有FragmentPagerAdapter，可以把News，Comments，Newset三个Activity放在Tab里显示

## Other
Model和Parser的设计不是很喜欢。。感觉应该有更好的方案。
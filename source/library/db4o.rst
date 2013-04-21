# 使用db4o对象数据库存储数据


## 对象数据库

我们现在主流的产品开发语言是面向对象的，移动开发更是如此。在进行数据存储的时候，往往会将大规模的数据存储到数据库中，一般是用关系型数据库。Android自带了对SQLite的运行库，提供对SQLite的支持。

但是，我们操作数据的时候，是把数据转换成对象的。每次存储，查询数据的过程中，进行转换操作，很不方便。虽然Android对数据库进行了一些封装，但是仍然有些麻烦的地方。

于是有了一些ORM（对象数据库映射）的类库，帮助我们转换。Android能使用的ORM库有

- Ormlite
- GreenDao
- Sugar（推荐）

对象数据库更方便，直接以以对象形式表示信息，而不是转换成一个二维表格及表格字段之间的关系。

更多介绍请看[维基百科  对象数据库](http://zh.wikipedia.org/wiki/%E5%AF%B9%E8%B1%A1%E6%95%B0%E6%8D%AE%E5%BA%93)

## db4o介绍

## db40使用

本地文档<file://localhost/Users/linxiangyu/source/android/lib/db4o-8.0/doc/tutorial/index.html>
最早看的文章<http://tech.ddvip.com/2008-02/120344698942042.html>
- <http://java.dzone.com/articles/using-db4o-android-application>
- <http://www.sohailaziz.com/2012/09/using-database-for-objects-db4o-in.html>
- <http://community.versant.com/documentation/reference/db4o-8.1/java/reference/Content/platform_specific_issues/android/getting_started.htm>
- 

## Proguard

http://community.versant.com/documentation/reference/db4o-8.1/java/reference/Content/platform_specific_issues/android/proguard.htm


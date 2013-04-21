

#Android单元测试的最佳实践


<http://stackoverflow.com/questions/522312/best-practices-for-unit-testing-android-apps>

## Question:


I'd like to unit test my Android application but I found that test driven development in Android is far from trivial at the moment.

Any tips, tricks, war stories for building light weight and preferably fast running tests?

我想要对我的Android程序进行单元测试，因为我发现测试驱动开发并不是那么繁琐。

有什么关于构建轻量的，又快友好的单元测试的故事或者技巧么？

## Answer:

- 你应该尝试[Robotium](https://code.google.com/p/robotium/)！去Robotium.org下载它的实例测试项目。Robotium是一个非常易用的测试框架，可以又方便又快地进行Android程序的测试。我创建了它，用来方便的进行测试Android应用。它适用于ActivityInstrumentationTestCase2结合。


- 同样值得去尝试下Robolectric,一个测试Android程序的选择。

Robolectric直接在你电脑的JVM上运行测试，而不是把应用和程序部署到你Android模拟器上面（那会很慢），减少了测试的时间


Robolectric允许你测试大多数Android的功能，包括布局和GUI行为，服务，网络代码。它在测试一些Android特性的时候比Gooogle的测试框架提供了更多的灵活性， 比如widgets 和 services.

---

##TODO

- Android 自带测试框架
- 用PowerMock构建Mock对象
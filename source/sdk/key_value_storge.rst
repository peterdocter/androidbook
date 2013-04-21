<http://stackoverflow.com/questions/5859818/what-is-the-best-mechanism-for-a-persistent-key-value-store-on-android-with-lar>

最好的Andorid持久性存储方案是什么？

我想在Android设备上用一个快的key-value存储，value是位图。存储需要备份在磁盘里并且提供缓存，最小化磁盘IO，希望多线程，但不需要。


我看Java Berkeley DB which 似乎符合要求，有其他的库更适合Android么？或者说Berkeley DB在Android上表现好么。


我可以写一个单单存储在内存里面的Key-value存储。。。但是数据大的话汇报内存的。



我建议用基于文件的内存缓存，不用重载设备的内存，不需要额外的东西，用数据库不会对你的需要有什么优化。

这里有一些库：


- <https://github.com/commonsguy/cwac-cache>
- <https://github.com/wareninja/generic-store-for-android> 

我用过它们做图片缓存，感觉不错哦。
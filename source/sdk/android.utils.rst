
# Android SDK android.utils包概述

这是Android SDK android.utils包的一些类的用处，更多内容详见：

<https://developer.android.com/reference/android/util/package-summary.html>
## AtomicFile


API17开始加入，源码+注释才200多行。

一个实现对文件进行原子操作的帮助类，通过创造一个备份文件直到成功写入来实现。如果你需要它在老的版本上使用的话，可以在V4 support库里面用AtomicFile。

AcomicFile没用锁，所以不要在用多进程或多线程并发访问的时候使用。


## Base64

帮助将二进制数据转码成base64格式的类

## Log

不必多说。。。输出日志

## FloatMath

类似于Math库，提供一些数学操作。不过直接用Float类型，不转换成Double，据说有些平台会快一些。


##  JsonReader JsonWriter

类似Gson等库，使用流式解析Json对象


## Pair

很简单的类，类似C++的Pair一样，一双对象的容器

## MonthDisplayHelper

帮助处理不同的月份有不同的天数的这类问题的。

## LongSparseArray

像要比HashMap<Long,Object>更有效率的一个类。。

## SparseArray

同上。。不过对手是HashMap<Integer, Object>

## SparseIntArray

同上，针对HashMap<Integer, Integer>

## SparseBooleanArray

同上，针对HashMap<Integer, Boolean>


## Xml

处理XML用的。

## TimingLogger

能显示打Log时间的Log。。

## TimeUtils

帮助解决时区相关操作

##StringBuilderPrinter

帮助你构建更适合打印的StringBuillder。。。核心代码就几行。。。
	
	  public void println(String x) {
	        mBuilder.append(x);
	        int len = x.length();
	        if (len <= 0 || x.charAt(len-1) != '\n') {
	            mBuilder.append('\n');
	        }
	    }


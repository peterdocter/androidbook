
Android调试  

Dev Tools 安装到 Android 实体机上

adb -e pull /system/app/Development.apk ./Development.apk
adb -d install Development.apk



查看内存占用

~ adb -d shell dumpsys meminfo
~ adb -e shell procrank

布局分析工具 lint

hierarchyviewer

monkey压力测试

monkeyrunner

＃AndroidUtils源码分析

## 文件结构

    ~/source/android/lib/AndroidUtils(master ✔) tree
    .
    ├── AndroidManifest.xml
    ├── README.md
    ├── assets
    │   └── fonts
    │       ├── Roboto-Black.ttf
    │       ├── Roboto-BlackItalic.ttf
    │       ├── Roboto-Bold.ttf
    │       ├── Roboto-BoldCondensed.ttf
    │       ├── Roboto-BoldCondensedItalic.ttf
    │       ├── Roboto-BoldItalic.ttf
    │       ├── Roboto-Condensed.ttf
    │       ├── Roboto-CondensedItalic.ttf
    │       ├── Roboto-Italic.ttf
    │       ├── Roboto-Light.ttf
    │       ├── Roboto-LightItalic.ttf
    │       ├── Roboto-Medium.ttf
    │       ├── Roboto-MediumItalic.ttf
    │       ├── Roboto-Regular.ttf
    │       ├── Roboto-Thin.ttf
    │       └── Roboto-ThinItalic.ttf
    ├── libs
    │   ├── android-support-v13.jar
    │   └── commons-io-2.4.jar
    ├── lint.xml
    ├── proguard-project.txt
    ├── project.properties
    ├── res
    │   └── values
    │       ├── attrs.xml
    │       └── donottranslate.xml
    └── src
        └── com
            └── michaelpardo
                ├── android
                │   ├── content
                │   │   ├── ImageManager.java
                │   │   └── TaskManager.java
                │   ├── net
                │   │   ├── JsonResponseHandler.java
                │   │   ├── ResponseBase.java
                │   │   ├── ServerError.java
                │   │   └── ServicesBase.java
                │   ├── text
                │   │   ├── AutoAdvanceTextWatcher.java
                │   │   ├── MaskFormatter.java
                │   │   └── MaskTextWatcher.java
                │   ├── util
                │   │   ├── AndroidUtils.java
                │   │   ├── IntentUtils.java
                │   │   ├── Log.java
                │   │   ├── NetworkUtils.java
                │   │   ├── SettingUtils.java
                │   │   ├── TypefaceUtils.java
                │   │   └── Ui.java
                │   └── widget
                │       ├── AutoResizeTextView.java
                │       ├── Button.java
                │       ├── EditText.java
                │       ├── ObservableHorizontalScrollView.java
                │       ├── ObservableScrollView.java
                │       ├── TextView.java
                │       └── chartview
                │           ├── AbstractSeries.java
                │           ├── ChartView.java
                │           ├── LabelAdapter.java
                │           ├── LinearSeries.java
                │           └── RectD.java
                └── java
                    └── text
                        └── ThreadSafeSimpleDateFormat.java

###第三方包文件

- 引入了 android-support-v13.jar 
- 熟悉Java的同学都应该熟悉的，Apache基金会的commons-io-2.4.jar包

### 内容
就是一个Android库项目，看类名是对一些常用操作的封装，包括文字，图片，网络等等，貌似还有一些自定义UI？此外，还有一堆字体文件。。。

有一个donottranslate.xml文件，内容是

    <?xml version="1.0" encoding="utf-8"?>
    <resources>

        <string name="release_signature" />

    </resources>

在AndroidUtils类中有一个  public static boolean isRelease(Context context) 方法，判断其是否为已经release的版本。

##源码分析

###  ThreadSafeSimpleDateFormat
package com.michaelpardo.java.text包下只有一个

    public class ThreadSafeSimpleDateFormat {
        private DateFormat mDateFormat;

        public ThreadSafeSimpleDateFormat(String format) {
            mDateFormat = new SimpleDateFormat(format);
        }

        public synchronized String format(Date date) {
            return mDateFormat.format(date);
        }

        public synchronized Date parse(String string) throws ParseException {
            return mDateFormat.parse(string);
        }
    }

弄了一个线程安全的SimpleDateFormat，好像就是把调用DateFormat的方法声明加上synchronized。。。









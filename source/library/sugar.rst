我觉得不好的地方：
主键用ID而不是Android需要的_id
SugarRecord可以加入更丰富的内容

#Android ORM库 -- Sugar源代码分析

Sugar是一个开源的Android ORM库，使用方便简单，源码不多，1800多行，是适合学习Android数据库处理的优秀开源项目。

Sugar库是一个Android Library Project，也有编译好的Jar文件。目录结构是

	~/source/android/lib/sugar(master ✔) tree    
	.
	├── LICENSE
	├── README.md
	├── dist
	│   ├── sugar-1.0.jar
	│   └── sugar-1.1.jar
	├── example
	│   ├── AndroidManifest.xml
	│   ├── build.properties
	│   ├── build.xml
	│   ├── default.properties
	│   ├── libs
	│   │   └── sugar-1.1.jar
	│   ├── local.properties
	│   ├── proguard.cfg
	│   ├── res
	│   │   ├── drawable-hdpi
	│   │   │   └── icon.png
	│   │   ├── drawable-ldpi
	│   │   │   └── icon.png
	│   │   ├── drawable-mdpi
	│   │   │   └── icon.png
	│   │   ├── layout
	│   │   │   ├── main.xml
	│   │   │   └── notelist.xml
	│   │   └── values
	│   │       └── strings.xml
	│   └── src
	│       └── com
	│           └── example
	│               ├── AddNoteActivity.java
	│               ├── Note.java
	│               ├── NoteListActivity.java
	│               ├── NoteRelation.java
	│               ├── SugarActivity.java
	│               └── Tag.java
	└── library
	    ├── AndroidManifest.xml
	    ├── build.properties
	    ├── build.xml
	    ├── project.properties
	    ├── res
	    │   └── values
	    │       └── strings.xml
	    ├── src
	    │   └── com
	    │       └── orm
	    │           ├── Database.java
	    │           ├── NumberComparator.java
	    │           ├── QueryBuilder.java
	    │           ├── StringUtil.java
	    │           ├── SugarApp.java
	    │           ├── SugarConfig.java
	    │           ├── SugarCursorFactory.java
	    │           ├── SugarDb.java
	    │           ├── SugarRecord.java
	    │           ├── dsl
	    │           │   ├── Collection.java
	    │           │   └── Ignore.java
	    │           └── query
	    │               ├── Condition.java
	    │               └── Select.java
	    └── test
	        └── com
	            └── orm
	                ├── StringUtilTest.java
	                └── query
	                    ├── DummyContext.java
	                    ├── SelectTest.java
	                    └── TestRecord.java
	                    
	                    
	                    
我们分析的是library目录下的文件。

AndroidManifest内容是

	<?xml version="1.0" encoding="utf-8"?>
	<manifest xmlns:android="http://schemas.android.com/apk/res/android"
	          package="com.orm.dsl"
	          android:versionCode="1"
	          android:versionName="1.0">
	    <uses-sdk android:minSdkVersion="4" />
	
	</manifest>	  
	
最低SDK要求为4，这意味着几乎可以在任何主流Android机型中使用。

project.properties文件声明了是一个Androdi Library项目。


	android.library=true
	# Project target.
	target=android-15
	
下面主要分析的是Src目录下的源代码

	~/source/android/lib/sugar/library/src(master ✔) tree
	.
	└── com
	    └── orm
	        ├── Database.java
	        ├── NumberComparator.java
	        ├── QueryBuilder.java
	        ├── StringUtil.java
	        ├── SugarApp.java
	        ├── SugarConfig.java
	        ├── SugarCursorFactory.java
	        ├── SugarDb.java
	        ├── SugarRecord.java
	        ├── dsl
	        │   ├── Collection.java
	        │   └── Ignore.java
	        └── query
	            ├── Condition.java
	            └── Select.java
	            
	            
我们使用Sugar的时候，每次都要在AndroidManifest中声明<application  android:name=".SugarApp>。 先来看看这个SugarApp有什么名堂吧～

	public class SugarApp extends android.app.Application{
	
	    Database database;
	    private static SugarApp sugarContext;
	
	    public void onCreate(){
	        super.onCreate();
	        SugarApp.sugarContext = this;
	        this.database = new Database(this);
	    }
	
	    public void onTerminate(){
	
	    if (this.database != null) {
	      this.database.closeDB();
	    }
	        super.onTerminate();
	    }
	
	    public static SugarApp getSugarContext(){
	        return sugarContext;
	    }
	
	
	}
		
		
SugarApp干了这些事情:

- 定义了一个Database对象，没有访问作用域声明，所以访问权限是默认的protected，即在与它同一个包(com.orm)中的类能访问。
- 在应用开始的时候初始化Database对象，应用结束的时候调用`this.database.closeDB();`关闭数据库。  
- 定义了`private static SugarApp sugarContext;`变量，在应用开始的时候将其初始化为自身；以及`public static SugarApp getSugarContext()`静态方法，返回应用的Context对象。

在Application中建一个静态方法，返回全局的Application Context对象，这一点我们经常在自己的程序中用到。因为在Android中，我们的一些操作必须调用Context对象。如果这些操作不在Context的子类如Service，Acticity之中，我们必须传入一个Context对象。在Application中定义之后，就可以直接调用SugarApp,getSugarContext()来获得。



再来看看这里定义的Database里面到底藏着什么～

	
	public class Database {
	    private SugarDb sugarDb;
	    private SQLiteDatabase sqLiteDatabase;
	
	    public Database(Context context){
	        this.sugarDb  = new SugarDb(context);
	    }
	
	
	  public SQLiteDatabase openDB() {
	    this.sqLiteDatabase = this.sugarDb.getWritableDatabase();
	
	    return this.sqLiteDatabase;
	  }
	
	  public void closeDB() {
	    if (this.sqLiteDatabase != null) {
	      this.sqLiteDatabase.close();
	      this.sqLiteDatabase = null;
	    }
	  }
	}
	
	
两个私有变量：SugarDb 以及  SQLiteDatabase. 后者是Android SDK提供的对SQLite数据库操作封装好的对象。在这里定义了关闭数据库操作的方法，以及一个`openDB（）`方法和`closeDB（）`，提供对SQLiteDatabase对象的初始化和关闭的操作接口。



再让我们追溯到SugarDb吧



SugarDb继承自SQLiteOpenHelper，此对象提供了对数据库的创建，升级等操作。

	public class SugarDb extends SQLiteOpenHelper 
	
定义一个Context对象。。。Android到处用到Context。。。

	    private Context context;
	    
这是一个构造方法，首先调用超类的构造方法，再初始化context对象。

	
	    public SugarDb(Context context) {
	        super(context, SugarConfig.getDatabaseName(context), new SugarCursorFactory(getDebugEnabled(context)), getDatabaseVersion(context));
	        this.context = context;
	
	    }
	    
我们看看超类，也就是SQLiteOpenHelper的构造方法的Android 官方文档：



	public SQLiteOpenHelper (Context context, String name, SQLiteDatabase.CursorFactory factory, int version)
	
	Since: API Level 1
	
	Create a helper object to create, open, and/or manage a database. This method always returns very quickly. The database is not actually created or opened until one of getWritableDatabase() or getReadableDatabase() is called.
	
	Parameters
	
	context
	
	to use to open or create the database
	
	name
	
	of the database file, or null for an in-memory database
	
	factory
	
	to use for creating cursor objects, or null for the default
	
	version
	
	number of the database (starting at 1); if the database is older, onUpgrade(SQLiteDatabase, int, int) will be used to upgrade the database; if the database is newer, onDowngrade(SQLiteDatabase, int, int) will be used to downgrade the database
	
	
其实也就是传入一个应用程序上下文，一个数据库文件的名字，一个构造Cursor的工厂对象，一个数据库版本。初始化SQLiteOpenHelper


 	
	


	
下面这几个方法是返回在你自己的的应用包里面所有继承自SugarRecord的类，以及其私有的一些帮助方法。其中一些关于Java反射机制用法可以学习一下，还有Android的PackageManager的使用方法值得学习。

	    private <T extends SugarRecord> List<T> getDomainClasses(Context context) {
	        List<T> domainClasses = new ArrayList<T>();
	        try {
	            Enumeration allClasses = getAllClasses(context);
	
	            while (allClasses.hasMoreElements()) {
	                String className = (String) allClasses.nextElement();
	
	                if (className.startsWith(SugarConfig.getDomainPackageName(context))) {
	                    T domainClass = getDomainClass(className, context);
	                    if (domainClass != null) domainClasses.add(domainClass);
	                }
	            }
	
	        } catch (IOException e) {
	            Log.e("Sugar", e.getMessage());
	        } catch (PackageManager.NameNotFoundException e) {
	            Log.e("Sugar", e.getMessage());
	        }
	
	        return domainClasses;
	  }
	


	    private <T extends SugarRecord> T getDomainClass(String className, Context context) {
	        Log.i("Sugar", "domain class");
	        Class discoveredClass = null;
	        try {
	            discoveredClass = Class.forName(className, true, context.getClass().getClassLoader());
	        } catch (ClassNotFoundException e) {
	            Log.e("Sugar", e.getMessage());
	        }
	
	        if ((discoveredClass == null) ||
	                (!SugarRecord.class.isAssignableFrom(discoveredClass)) ||
	                Modifier.isAbstract(discoveredClass.getModifiers())) {
	            return null;
	        } else {
	            try {
	                return (T) discoveredClass.getDeclaredConstructor(Context.class).newInstance(context);
	            } catch (InstantiationException e) {
	                Log.e("Sugar", e.getMessage());
	            } catch (IllegalAccessException e) {
	                Log.e("Sugar", e.getMessage());
	            } catch (NoSuchMethodException e) {
	                Log.e("Sugar", e.getMessage());
	            } catch (InvocationTargetException e) {
	                Log.e("Sugar", e.getMessage());
	            }
	        }
	
	        return null;
	
	    }
	
	    private Enumeration getAllClasses(Context context) throws PackageManager.NameNotFoundException, IOException {
	        String path = getSourcePath(context);
	        DexFile dexfile = new DexFile(path);
	        return dexfile.entries();
	    }
	
	    private String getSourcePath(Context context) throws PackageManager.NameNotFoundException {
	        return context.getPackageManager().getApplicationInfo(context.getPackageName(), 0).sourceDir;
	    }
	
onCreate方法，初始化数据库。为什么Sugar能根据我们自己定义的类的对象生成数据库？秘密就在这里

	    @Override
	    public void onCreate(SQLiteDatabase sqLiteDatabase) {
	        Log.i("Sugar", "on create");
	        createDatabase(sqLiteDatabase);
	    }
	    

	
	    private <T extends SugarRecord> void createDatabase(SQLiteDatabase sqLiteDatabase) {
	        List<T> domainClasses = getDomainClasses(context);
	        for (T domain : domainClasses) {
	            createTable(domain, sqLiteDatabase);
	        }
	    }
	    
onCreate方法其实是再调用createDatabase，对于每一个用刚才定义的getDomainClasses()方法找到的继承自SugarRecord的类，进行表格创建工作。
	
	    private <T extends SugarRecord> void createTable(T table, SQLiteDatabase sqLiteDatabase) {
	        Log.i("Sugar", "create table");
	        List<Field> fields = table.getTableFields();
	        StringBuilder sb = new StringBuilder("CREATE TABLE ").append(table.getSqlName()).append(
	                " ( ID INTEGER PRIMARY KEY AUTOINCREMENT ");
	
	        for (Field column : fields) {
	            String columnName = StringUtil.toSQLName(column.getName());
	            String columnType = QueryBuilder.getColumnType(column.getType());
	
	            if (columnType != null) {
	
	                if (columnName.equalsIgnoreCase("Id")) {
	                    continue;
	                }
	                sb.append(", ").append(columnName).append(" ").append(columnType);
	            }
	        }
	        sb.append(" ) ");
	
	        Log.i("Sugar", "creating table " + table.getSqlName());
	
	        if (!"".equals(sb.toString()))
	            sqLiteDatabase.execSQL(sb.toString());
	    }

	
	
createTable方法根据SugarRecord的子类的字段的信息，动态生成SQL的CREATE TABLE语句。并且自动帮我们加入了ID字段：ID INTEGER PRIMARY KEY AUTOINCREMENT

再看看是如何处理升级数据库的：

	
	    @Override
	    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int oldVersion, int newVersion) {
	        Log.i("Sugar", "upgrading sugar");
	        if (!executeSugarUpgrade(sqLiteDatabase, oldVersion, newVersion)) {
	            deleteTables(sqLiteDatabase);
	            onCreate(sqLiteDatabase);
	        }
	    }
	    
	    	    private <T extends SugarRecord> void deleteTables(SQLiteDatabase sqLiteDatabase) {
	        List<T> tables = getDomainClasses(this.context);
	        for (T table : tables) {
	            sqLiteDatabase.execSQL("DROP TABLE IF EXISTS " + table.getSqlName());
	        }
	    }
	    
	    
很简单。。如果自定义的数据库升级操作不成功就删掉重建。。

自定义	升级操作是这样的：

通过`List<String> files = Arrays.asList(this.context.getAssets().list("sugar_upgrades"));`找到Asset/sugar_upgrades/下的所有文件，然后依次打开文件，	        用`db.execSQL(text.toString());`
执行里面的SQL语句

	    private boolean executeSugarUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
	
	        boolean isSuccess = false;
	        try {
	            List<String> files = Arrays.asList(this.context.getAssets().list("sugar_upgrades"));
	            Collections.sort(files, new NumberComparator());
	
	            for (String file : files){
	                Log.i("Sugar", "filename : " + file);
	                try {
	                    int version = Integer.valueOf(file.replace(".sql", ""));
	
	                    if ((version > oldVersion) && (version <= newVersion)) {
	                        executeScript(db, file);
	                        isSuccess = true;
	                    }
	                } catch (NumberFormatException e) {
	                    Log.i("Sugar", "not a sugar script. ignored." + file);
	                }
	            }
	        } catch (IOException e) {
	            Log.e("Sugar", e.getMessage());
	        }
	
	        return isSuccess;
	    }
	
	    private void executeScript(SQLiteDatabase db, String file) {
	        StringBuilder text = new StringBuilder();
	        try {
	            InputStream is = this.context.getAssets().open("sugar_upgrades/" + file);
	            BufferedReader reader = new BufferedReader(new InputStreamReader(is));
	            String line;
	            while ((line = reader.readLine()) != null) {
	                text.append(line);
	                text.append("\n");
	            }
	        } catch (IOException e) {
	            Log.e("Sugar", e.getMessage());
	        }
	
	        Log.i("Sugar", "script : " + text.toString());
	        db.execSQL(text.toString());
	    }
	}
	
	
现在回过头来看看数据库CREATE TABLE语句的创建过程


			
        StringBuilder sb = new StringBuilder("CREATE TABLE ").append(table.getSqlName()).append(" ( ID INTEGER PRIMARY KEY AUTOINCREMENT ");

        for (Field column : fields) {
            String columnName = StringUtil.toSQLName(column.getName());
            String columnType = QueryBuilder.getColumnType(column.getType());

            if (columnType != null) {

                if (columnName.equalsIgnoreCase("Id")) {
                    continue;
                }
                sb.append(", ").append(columnName).append(" ").append(columnType);
            }
        }
        sb.append(" ) ");
        
        
用了自定义的StringUtil和QueryBuilder类来帮助创建CREATE TABLE语句


             String columnName = StringUtil.toSQLName(column.getName());
             String columnType = QueryBuilder.getColumnType(column.getType());    
            
            
            
 来看看这两个里面有什么  
 
          
	package com.orm;
	
	public class StringUtil {
	    public static String toSQLName(String javaNotation) {
	            if(javaNotation.equalsIgnoreCase("_id"))
	                    return "_id";
	
	            StringBuilder sb = new StringBuilder();
	            char[] buf = javaNotation.toCharArray();
	
	            for (int i = 0; i < buf.length; i++) {
	                    char prevChar = (i > 0) ? buf[i - 1] : 0;
	                    char c = buf[i];
	                    char nextChar = (i < buf.length - 1) ? buf[i + 1] : 0;
	                    boolean isFirstChar = (i == 0);
	
	                    if (isFirstChar || Character.isLowerCase(c)) {
	                            sb.append(Character.toUpperCase(c));
	                    } else if (Character.isUpperCase(c)) {
	                            if (Character.isLetterOrDigit(prevChar)) {
	                                    if (Character.isLowerCase(prevChar)) {
	                                            sb.append('_').append(Character.toUpperCase(c));
	                                    } else if (nextChar > 0 && Character.isLowerCase(nextChar)) {
	                                            sb.append('_').append(Character.toUpperCase(c));
	                                    } else {
	                                            sb.append(c);
	                                    }
	                            }
	                            else {
	                                    sb.append(c);
	                            }
	                    }
	            }
	
	            return sb.toString();
	    }
	
	}
	   	              
就是把定义的数据库字段名换成作者先要的格式，规则是：

- 第一个字母不变
- 之后如果是某大写字母X的，换成_X（前面加下划线）

我不明白的是为什么会有对_id的处理？

  	      
  	       if(javaNotation.equalsIgnoreCase("_id"))
	                    return "_id";
	                    
	                    
作者明明忽略了使用者对ID字段的创建，并自动加入了ID主键。。。


再看看QueryBuilder

	package com.orm;
	
	public class QueryBuilder {
	
	    public static String getColumnType(Class type) {
	        if ((type.equals(Boolean.class)) ||
	                (type.equals(Boolean.TYPE)) ||
	                (type.equals(java.util.Date.class)) ||
	                (type.equals(java.sql.Date.class)) ||
	                (type.equals(Integer.class)) ||
	                (type.equals(Integer.TYPE)) ||
	                (type.equals(Long.class)) ||
	                (type.equals(Long.TYPE)) || (
	                (!type.isPrimitive()) &&
	                        (type.getSuperclass() != null) &&
	                        (type.getSuperclass().equals(SugarRecord.class)))) {
	            return "INTEGER";
	        }
	
	        if ((type.equals(Double.class)) || (type.equals(Double.TYPE)) || (type.equals(Float.class)) ||
	                (type.equals(Float.TYPE))) {
	            return "FLOAT";
	        }
	
	        if ((type.equals(String.class)) || (type.equals(Character.TYPE))) {
	            return "TEXT";
	        }
	
	        return "";
	    }
	}
	
SQLite支持的数据类型不多，所以Sugar把Boolean, Integer, Long, 以及java.util.Date 和 java.sql.Date，还有SugarRecord的子类转换成了INTEGER类型，Double以及Float类型转换成了FLOAT，String转化成了TEXT,其他类型忽略。


现在看看SugarConfig类：


	public class SugarConfig {
	
	    static Map<Class,List<Field>> fields = new HashMap<Class, List<Field>>();
	
	    public static String getDatabaseName(Context context) {
	        String databaseName = getMetaDataString(context, "DATABASE");
	
	        if (databaseName == null) {
	            databaseName = "Sugar.db";
	        }
	
	        return databaseName;
	    }
	
	    public static void setFields(Class clazz, List<Field> fieldz){
	         fields.put(clazz, fieldz);
	    }
	
	    public static List<Field> getFields(Class clazz){
	
	        if(fields.containsKey(clazz)){
	            List<Field> list = fields.get(clazz);
	            return Collections.synchronizedList(list);
	        }
	
	        return null;
	    }
	
	    public static void clearCache(){
	        fields.clear();
	        fields = new HashMap<Class, List<Field>>();
	    }
	
	    public static int getDatabaseVersion(Context context) {
	        Integer databaseVersion = getMetaDataInteger(context, "VERSION");
	
	        if ((databaseVersion == null) || (databaseVersion == 0)) {
	            databaseVersion = 1;
	        }
	
	        return databaseVersion;
	    }
	
	    public static String getDomainPackageName(Context context){
	        String domainPackageName = getMetaDataString(context, "DOMAIN_PACKAGE_NAME");
	
	        if (domainPackageName == null) {
	            domainPackageName = "";
	        }
	
	        return domainPackageName;
	    }
	
	    public static boolean getDebugEnabled(Context context) {
	        return getMetaDataBoolean(context, "QUERY_LOG");
	    }
	
	    public static String getMetaDataString(Context context, String name) {
	        String value = null;
	
	        PackageManager pm = context.getPackageManager();
	        try {
	            ApplicationInfo ai = pm.getApplicationInfo(context.getPackageName(), 128);
	            value = ai.metaData.getString(name);
	        } catch (Exception e) {
	            Log.d("sugar", "Couldn't find config value: " + name);
	        }
	
	        return value;
	    }
	
	    public static Integer getMetaDataInteger(Context context, String name) {
	        Integer value = null;
	
	        PackageManager pm = context.getPackageManager();
	        try {
	            ApplicationInfo ai = pm.getApplicationInfo(context.getPackageName(), 128);
	            value = ai.metaData.getInt(name);
	        } catch (Exception e) {
	            Log.d("sugar", "Couldn't find config value: " + name);
	        }
	
	        return value;
	    }
	
	    public static Boolean getMetaDataBoolean(Context context, String name) {
	        Boolean value = false;
	
	        PackageManager pm = context.getPackageManager();
	        try {
	            ApplicationInfo ai = pm.getApplicationInfo(context.getPackageName(), 128);
	            value = ai.metaData.getBoolean(name);
	        } catch (Exception e) {
	            Log.d("sugar", "Couldn't find config value: " + name);
	        }
	
	        return value;
	    }
	}

可以看到，可以通过

	ApplicationInfo ai = pm.getApplicationInfo(context.getPackageName(), 128);
	value = ai.metaData.getBoolean(name);
	
	
这样的方式读取Manifest中自定义的META-DATA元信息中的数据库配置信息。

现在看看SugarCursorFactory


	public class SugarCursorFactory implements SQLiteDatabase.CursorFactory {
	
	    private boolean debugEnabled;
	
	    public SugarCursorFactory() {
	        this.debugEnabled = false;
	    }
	
	    public SugarCursorFactory(boolean debugEnabled){
	
	        this.debugEnabled = debugEnabled;
	    }
	
	    public Cursor newCursor(SQLiteDatabase sqLiteDatabase, SQLiteCursorDriver sqLiteCursorDriver, String editTable, SQLiteQuery sqLiteQuery) {
	
	        if(debugEnabled){
	            Log.d("SQL Log", sqLiteQuery.toString());
	        }
	
	        return new SQLiteCursor(sqLiteDatabase, sqLiteCursorDriver, editTable, sqLiteQuery);
	    }
	}
	
其实我之前也不是清楚这个SQLiteDatabase.CursorFactory是干什么用的。。

查阅SDK文档：

	SQLiteDatabase.CursorFactory
	
	android.database.sqlite.SQLiteDatabase.CursorFactory
	
	Class Overview

	Used to allow returning sub-classes of Cursor when calling query.
	
原来是生成Cursor的工厂方法啊。。

看看这个类的内容。。额。。原来只是设置一个debugEnabled变量。。。然后如果是调试模式的话。。每次查询的时候就Log打印查询语句额。。。。

不过。。这里用的` new SQLiteCursor(sqLiteDatabase, sqLiteCursorDriver, editTable, sqLiteQuery);`这个构造函数。。。SDK文档显示它已经被弃用了额。。。

	
	This constructor is deprecated.
	use SQLiteCursor(SQLiteCursorDriver, String, SQLiteQuery) instead
	
再看看NumberComparator类


	package com.orm;
	
	import java.util.Comparator;
	
	public class NumberComparator implements Comparator<Object> {
	
	    int compareRight(String a, String b) {
	        int bias = 0;
	        int ia = 0;
	        int ib = 0;
	        while (true) {
	            char ca = charAt(a, ia);
	            char cb = charAt(b, ib);
	
	            if ((!Character.isDigit(ca)) && (!Character.isDigit(cb))) {
	                return bias;
	            }
	            if (!Character.isDigit(ca)) {
	                return -1;
	            }
	            if (!Character.isDigit(cb)) {
	                return 1;
	            }
	            if (ca < cb) {
	                if (bias == 0) {
	                    bias = -1;
	                }
	            } else if (ca > cb) {
	                if (bias == 0)
	                    bias = 1;
	            } else if ((ca == 0) && (cb == 0))
	                return bias;
	            ia++;
	            ib++;
	        }
	    }
	
	    public int compare(Object o1, Object o2) {
	        String a = o1.toString();
	        String b = o2.toString();
	
	        int ia = 0;
	        int ib = 0;
	        int nza = 0;
	        int nzb = 0;
	        while (true) {
	            nza = nzb = 0;
	
	            char ca = charAt(a, ia);
	            char cb = charAt(b, ib);
	
	            while ((Character.isSpaceChar(ca)) || (ca == '0')) {
	                if (ca == '0') {
	                    nza++;
	                } else {
	                    nza = 0;
	                }
	
	                ca = charAt(a, ++ia);
	            }
	
	            while ((Character.isSpaceChar(cb)) || (cb == '0')) {
	                if (cb == '0') {
	                    nzb++;
	                } else {
	                    nzb = 0;
	                }
	
	                cb = charAt(b, ++ib);
	            }
	            int result;
	            if ((Character.isDigit(ca)) && (Character.isDigit(cb)) &&
	                    ((result = compareRight(a.substring(ia), b.substring(ib))) != 0)) {
	                return result;
	            }
	
	            if ((ca == 0) && (cb == 0)) {
	                return nza - nzb;
	            }
	
	            if (ca < cb) {
	                return -1;
	            }
	            if (ca > cb) {
	                return 1;
	            }
	
	            ia++;
	            ib++;
	        }
	    }
	
	    static char charAt(String s, int i) {
	        if (i >= s.length()) {
	            return '\000';
	        }
	
	        return s.charAt(i);
	    }
	}

实现了Comparator接口，用于再数据库升级过程中读取Asset文件夹下面多个文件的排序。具体的内容以后再看吧。


现在看看每次使用的时候都要继承的SugarRecord类


使用了范型，构造函数中初始化了Context，SugarApp，Database这些对象。	
	
	public class SugarRecord<T> {
	
	    @Ignore
	    private Context context;
	    @Ignore
	    private SugarApp application;
	    @Ignore
	    private Database database;
	    @Ignore
	    String tableName = getSqlName();
	    
	    protected Long id = null;
	    
	    public SugarRecord(Context context) {
	        this.context = context;
	        this.application = (SugarApp) context.getApplicationContext();
	        this.database = application.database;
	    }
	

delete操作，打开数据库，删除它对应的字段。。。关闭数据库。。。


	    public void delete() {
	        SQLiteDatabase db = this.database.openDB();
	        db.delete(this.tableName, "Id=?", new String[]{getId().toString()});
	        this.database.closeDB();
	
	    }

deleteAll操作，删除所有的字段，或者删除所有符合自定义where语句的字段
	
	    public static <T extends SugarRecord> void deleteAll(Class<T> type) {
	        Database db = getSugarContext().database;
	        SQLiteDatabase sqLiteDatabase = db.openDB();
	        sqLiteDatabase.delete(getTableName(type), null, null);
	    }
	
	    public static <T extends SugarRecord> void deleteAll(Class<T> type, String whereClause, String... whereArgs ) {
	        Database db = getSugarContext().database;
	        SQLiteDatabase sqLiteDatabase = db.openDB();
	        sqLiteDatabase.delete(getTableName(type), whereClause, whereArgs);
	    }
	
save操作，	打开数据库，动态获取字段名，然后插入。


	    public void save() {
	        SQLiteDatabase sqLiteDatabase = database.openDB();
	        List<Field> columns = getTableFields();
	        ContentValues values = new ContentValues(columns.size());
	        for (Field column : columns) {
	            column.setAccessible(true);
	            try {
	                if (SugarRecord.class.isAssignableFrom(column.getType())) {
	                    values.put(StringUtil.toSQLName(column.getName()),
	                            (column.get(this) != null)
	                                    ? String.valueOf(((SugarRecord) column.get(this)).id)
	                                    : "0");
	                } else {
	                    if (!"id".equalsIgnoreCase(column.getName())) {
	                        values.put(StringUtil.toSQLName(column.getName()),
	                                String.valueOf(column.get(this)));
	                    }
	                }
	
	            } catch (IllegalAccessException e) {
	                Log.e("Sugar", e.getMessage());
	            }
	        }
	
	        if (id == null)
	                id = sqLiteDatabase.insert(getSqlName(), null, values);
	        else
	                sqLiteDatabase.update(getSqlName(), values, "ID = ?", new String[]{String.valueOf(id)});
	
	        Log.i("Sugar", getClass().getSimpleName() + " saved : " + id);
	        database.closeDB();
	    }
	
	
列出和删除操作，都是对SDK提供的用法更高级别的抽象和封装


	    public static <T extends SugarRecord> List<T> listAll(Class<T> type) {
	        return find(type, null, null, null, null, null);
	    }
	
	    public static <T extends SugarRecord> T findById(Class<T> type, Long id) {
	        List<T> list = find( type, "id=?", new String[]{String.valueOf(id)}, null, null, "1");
	        if (list.isEmpty()) return null;
	        return list.get(0);
	    }
	
	    public static <T extends SugarRecord> List<T> find(Class<T> type,
	                                                       String whereClause, String... whereArgs) {
	        return find(type, whereClause, whereArgs, null, null, null);
	    }
	
	    public static <T extends SugarRecord> List<T> findWithQuery(Class<T> type, String query, String... arguments){
	
	        Database db = getSugarContext().database;
	        SQLiteDatabase sqLiteDatabase = db.openDB();
	        T entity;
	        List<T> toRet = new ArrayList<T>();
	        Cursor c = sqLiteDatabase.rawQuery(query, arguments);
	
	        try {
	            while (c.moveToNext()) {
	                entity = type.getDeclaredConstructor(Context.class).newInstance(getSugarContext());
	                entity.inflate(c);
	                toRet.add(entity);
	            }
	        } catch (Exception e) {
	            e.printStackTrace();
	        } finally {
	            c.close();
	        }
	        return toRet;
	    }
	
	    public static void executeQuery(String query, String... arguments){
	        getSugarContext().database.openDB().execSQL(query, arguments);
	    }
	
	    public static <T extends SugarRecord> List<T> find(Class<T> type,
	                                                       String whereClause, String[] whereArgs,
	                                                       String groupBy, String orderBy, String limit) {
	        Database db = getSugarContext().database;
	        SQLiteDatabase sqLiteDatabase = db.openDB();
	        T entity;
	        List<T> toRet = new ArrayList<T>();
	        Cursor c = sqLiteDatabase.query(getTableName(type), null,
	                whereClause, whereArgs, groupBy, null, orderBy, limit);
	        try {
	            while (c.moveToNext()) {
	                entity = type.getDeclaredConstructor(Context.class).newInstance(getSugarContext());
	                entity.inflate(c);
	                toRet.add(entity);
	            }
	        } catch (Exception e) {
	            e.printStackTrace();
	        } finally {
	            c.close();
	        }
	        return toRet;
	    }
	
	
inflate操作，用反射机制，提供通用的方法，把Cursor的内容转化为对象


	    void inflate(Cursor cursor) {
	        Map<Field, Long> entities = new HashMap<Field, Long>();
	        List<Field> columns = getTableFields();
	        for (Field field : columns) {
	            field.setAccessible(true);
	            try {
	                String typeString = field.getType().getName();
	                String colName = StringUtil.toSQLName(field.getName());
	
	                if(colName.equalsIgnoreCase("id")){
	                    long cid = cursor.getLong(cursor.getColumnIndex(colName));
	                    field.set(this, Long.valueOf(cid));
	                }else if (typeString.equals("long")) {
	                    field.setLong(this,
	                            cursor.getLong(cursor.getColumnIndex(colName)));
	                } else if (typeString.equals("java.lang.String")) {
	                    String val = cursor.getString(cursor
	                            .getColumnIndex(colName));
	                    field.set(this, val.equals("null") ? null : val);
	                } else if (typeString.equals("double")) {
	                    field.setDouble(this,
	                            cursor.getDouble(cursor.getColumnIndex(colName)));
	                } else if (typeString.equals("boolean")) {
	                    field.setBoolean(this,
	                            cursor.getString(cursor.getColumnIndex(colName))
	                                    .equals("true"));
	                } else if (typeString.equals("[B")) {
	                    field.set(this,
	                            cursor.getBlob(cursor.getColumnIndex(colName)));
	                } else if (typeString.equals("int")) {
	                    field.setInt(this,
	                            cursor.getInt(cursor.getColumnIndex(colName)));
	                } else if (typeString.equals("float")) {
	                    field.setFloat(this,
	                            cursor.getFloat(cursor.getColumnIndex(colName)));
	                } else if (typeString.equals("short")) {
	                    field.setShort(this,
	                            cursor.getShort(cursor.getColumnIndex(colName)));
	                } else if (typeString.equals("java.sql.Timestamp")) {
	                    long l = cursor.getLong(cursor.getColumnIndex(colName));
	                    field.set(this, new Timestamp(l));
	                } else if (SugarRecord.class.isAssignableFrom(field.getType())) {
	                    long id = cursor.getLong(cursor.getColumnIndex(colName));
	                    if (id > 0)
	                        entities.put(field, id);
	                    else
	                        field.set(this, null);
	                } else
	                    Log.e("Sugar", "Class cannot be read from Sqlite3 database.");
	            } catch (IllegalArgumentException e) {
	                Log.e("field set error", e.getMessage());
	            } catch (IllegalAccessException e) {
	                Log.e("field set error", e.getMessage());
	            }
	
	        }
	
	        for (Field f : entities.keySet()) {
	            try {
	                f.set(this, findById((Class<? extends SugarRecord>) f.getType(),
	                        entities.get(f)));
	            } catch (SQLiteException e) {
	            } catch (IllegalArgumentException e) {
	            } catch (IllegalAccessException e) {
	            }
	        }
	    }
	
	    public List<Field> getTableFields() {
	        List<Field> fieldList = SugarConfig.getFields(getClass());
	        if(fieldList != null) return fieldList;
	
	        Log.d("Sugar", "Fetching properties");
	        List<Field> typeFields = new ArrayList<Field>();
	        
	        getAllFields(typeFields, getClass());
	        
	        List<Field> toStore = new ArrayList<Field>();
	        for (Field field : typeFields) {
	            if (!field.isAnnotationPresent(Ignore.class)) {
	                toStore.add(field);
	            }
	        }
	
	        SugarConfig.setFields(getClass(), toStore);
	        return toStore;
	    }
	    
	    private static List<Field> getAllFields(List<Field> fields, Class<?> type) {
	        for (Field field : type.getDeclaredFields()) {
	            fields.add(field);
	        }
	
	        if (type.getSuperclass() != null) {
	            fields = getAllFields(fields, type.getSuperclass());
	        }
	
	        return fields;
	    }
	
	    public String getSqlName() {
	        return getTableName(getClass());
	    }
	
	
	    public static String getTableName(Class<?> type) {
	        return StringUtil.toSQLName(type.getSimpleName());
	    }
	
	    public Long getId() {
	        return id;
	    }
	
	    public void setId(Long id) {
	        this.id = id;
	    }
	}


有了其它类的帮助。。SugarRecord似乎也没什么内容。。很好理解

下面看看com.orm.dsl包有什么内容。。
	
	package com.orm.dsl;
	import java.util.*;
	
	public class Collection {
	
	    public static <T> List<T> list(T... args) {
	        return Arrays.asList(args);
	    }
	
	    public static <T> Set<T> set(T... args) {
	        Set<T> result = new HashSet<T>(args.length);
	        result.addAll(Arrays.asList(args));
	        return result;
	    }
	
	    public static <K, V> Map<K, V> map(Entry<? extends K, ? extends V>... entries) {
	        Map<K, V> result = new HashMap<K, V>(entries.length);
	
	        for (Entry<? extends K, ? extends V> entry : entries)
	            if (entry.value != null)
	                result.put(entry.key, entry.value);
	
	        return result;
	    }
	
	    public static <K, V> Entry<K, V> entry(K key, V value) {
	        return new Entry<K, V>(key, value);
	    }
	
	    public static class Entry<K, V> {
	        K key;
	        V value;
	
	        public Entry(K key, V value) {
	            this.key = key;
	            this.value = value;
	        }
	    }
	}


这个。。。只有再SugarApp里面有一句`import static com.orm.dsl.Collection.set;
`用到这个类。。。

或许作者想实现一个自己的Collenction。。以后用到吧。。。毕竟Sugar的源代码在我写这些文字的时候还在活跃的更新中。。。

还有Ignore注解。。

	package com.orm.dsl;
	
	import java.lang.annotation.Retention;
	import java.lang.annotation.RetentionPolicy;
	
	@Retention(RetentionPolicy.RUNTIME)
	public @interface Ignore {
	}
	
Java的注解，也就是代码里面@XXXX可以用来实现一些工作，不过我不是很清楚用法。现在在动车上也不好上网查。。

Query包里面有两个类：

	public class Select<T extends SugarRecord> 
	public class Condition 
	
提供了对SQL操作的封装。。不过只有在附带的Test用到。。只是作者为了测试方便写的。。实际的Sugar核心操作与这个没关系。。就不仔细看了。。


---

Sugar的代码还是很漂亮的。1800多行，把Java的反射，范型与Android的数据库API结合的很好。如果早点碰到，在北京实习的时候，折腾了20天数据库操作的时候，代码就会写的漂亮很多。。。不会弄到自己都不想用的地步。。。。


     
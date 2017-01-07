# 数据存储 #

主要有以下几种数据存储方式：
- Preference
- File
- SQLite
- 网络

## Preference ##
>主要应用在数据量比较少的情况下，以"key-value"方式保存在一个XML文件中

### 使用步骤 ###

- 使用getSharedPreferences(String XMLName,Mode mode)生成SharedPreferences对象
- 使用SharedPreferences.Editor的putXXX方法保存数据
- 使用SharedPreferences.Editor的commit()方法将上一步保存的数据写到XML文件中
- 使用SharedPreferences的getXXX()方法获取相应的数据

>

	//新建,Mode主要有MODE_PRIVATE,MODE_WORLD_READABLE,MODE_WORLD_WRITEABLE
    SharedPreferences sharedPreferences = getSharedPreferences("",Context.MODE_PRIVATE);
	
	//写入数据需要用到Editor
	Editor editor = sharedPreferences.edit();
	editor.putInt("key",123);
	editor.remove("key");
	editor.clear();
	boolean ret = editor.commit();

	//取出数据,后为defValue
	int value = sharedPreferences.getInt("key",0);
	Map map = sharedPreferences.getAll();
	//注册preference发生变化的监听函数
	sharedPreferences.registerOnSharedPreferenceChangeListener(Listener);

## File ##
>不受类型限制，可以将一些数据直接以文件的形式保存在设备中，如文本文件、PDF、音频、图片等

>通过Context.openFileInput()方法获取FileInputStream

>通过Context.openFileOutput()获取FileOutputStream

    String file = "testFile.txt"
	//获取指定文件的文件输入流
	FileInputStream fileInputStream = openFileInput(file);
	//定义缓存数组
	byte[] buffer = new bute[fileInputStream.available()];
	//将数据读入到缓存区
	fileInputStream.read(buffer);
	//关闭文件输入流
	fileInputStream.close();

>

    FileOutputStream fileOutputStream = openFileOutput(file,Context.MODE_PRIVATE);
	fileOutputStream.write("StringToBeWritten".getBytes());
	fileOutputStream.close();

## SQLite ##
>Android中通过SQLite数据库实现结构化的数据存储。SQLite是一个嵌入式数据库引擎，目的在于为内存等资源有限的设备，在数据的增删改查上提供一种高效的办法。

### SQLiteDatabase ###
	//打开或创建数据库
    openOrCreateDatabase(String path,SQLiteDatabase.CursorFactory factory);
	//打开数据库
	//其中flag可以为OPEN_READONLY,OPEN_READWRITE,CREATE_IF_NECESSARY,NO_LOCALIZED_COLLATORS
	openDatabase(String path,SQLiteDatabase.CursorFactory factory,int flags);
	//关闭数据库
	close();
	//插入一条记录
	insert(String table,String nullColumnHack,ContentValues values);
	//删除一条记录
	delete(String table,String whereClause,String[] whereArgs);
	//查询记录
	query(boolean distinct,String table,String[] columns,String selection,String[] selectionArgs,String groupBy,String having,String orderBy,String limit);
	//修改记录
	update(String table,ContextValues value,String whereClause,String[] whereArgs)
	//执行SQL语句
	execSQL(String sql)

#### ContentValues ####
>封装了列名称和列值的Map，代表一条记录信息

    ContentValues contentValues = new ContentValues();
	contentValues.put("ID",1);
	contentValues.put("age",22);
	contentValues.put("name","Dendi");
	sqliteDatabase.insert("student",null,contentValues);

#### Query操作和Cursor ####

    public Cursor query(boolean distinct,
		String table,
		String[] columns,
		String selection,//where字句，可以包含?通配符
		String[] selectionArgs,//参数数据，用来替换where子句中的?
		String groupBy,
		String having,
		String orderBy,
		String limit
	)

>Cursor

>提供了一种对从表中检索出的数据进行操作的灵活手段，它实际上是一种能从包括多条数据记录的结果集中每次提取一条记录的机制。

	//移动Cursor
    move(int offset)
	//
	moveToPosition(int position)
	moveToNext()
	moveToLast()
	moveToFirst()

	isFirst()
	isLast()
	isBeforeFirst()
	isAfterLast()

	isClosed()
	
	isNull(int columnIndex)
	getCount()

	getInt(int columnIndex)
	getString(int columnIndex)

### SQLiteOpenHelper ###
>是SQLiteDatabase的一个帮助类，用来管理数据库的创建和版本的更新

>一般使用的方式实现

    SQLiteOpenHelper(Context context,String name.SQLiteDatabase.CursorFactory,int version)
	onCreate(SQLiteDatabase db);
	onUpgrade(SQLiteDatabase db,int oldVersion,int newVersion)
	getReadableDatabase()
	getWritableDatabase()

## ContentProvider ##
>可访问Android提供的应用数据，如联系人列表。

>作为应用程序之间唯一的共享数据的途径，ContentProvider的主要功能是存储、检索数据，并向应用程序提供访问数据的接口。

    insert(Uri,ContentValues)
	delete(Uri,String,String[])
	update(Uri,ContentValues,String,String[])
	query(Uri,String[],String,String[],String)
	//获得MIME数据类型
	getType(Uri)
	//创建时调用
	onCreate()
	getContext()
	
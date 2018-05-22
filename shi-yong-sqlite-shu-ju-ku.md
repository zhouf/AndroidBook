# 在应用中使用SQLite数据库

> 实验目标
>
> 学习使用Android平台数据库，掌握SQLite基本的操作方法，将汇率数据保存在数据库中

### 第一步：创建数据库访问对象

创建类，继承于SQLiteOpenHelper，用于创建，更新数据库，获取数据库连接，此处创建DBHelper，代码如下

```
public class DBHelper extends SQLiteOpenHelper {

	private static final int VERSION = 1;
	private static final String DB_NAME = "myrate.db";
	public static final String TB_NAME = "tb_rates";
	
	
	public DBHelper(Context context, String name, CursorFactory factory,
			int version) {
		super(context, name, factory, version);
	}
	public DBHelper(Context context) {
		super(context,DB_NAME,null,VERSION);
	}

	@Override
	public void onCreate(SQLiteDatabase db) {
		db.execSQL("CREATE TABLE "+TB_NAME+"(ID INTEGER PRIMARY KEY AUTOINCREMENT,CURNAME TEXT,CURRATE TEXT)");
	}

	@Override
	public void onUpgrade(SQLiteDatabase arg0, int arg1, int arg2) {
		// TODO Auto-generated method stub
	}

}
```

默认版本号为1，数据库名为myrate.db，存放汇率数据的数据库表名为tb\_rates，系统第一次访问数据库时，没有会自动创建数据库，并创建数据库表，第一次使用时时间会长一点。onCreate方法主要是执行初始时创建表的语句，onUpdate方法是处理程序升级时的创建表语句。此处创建有三列数据，ID,CURNAME,CURRATE，如果不需要计算汇率，只是保存一下，用文本类型也是可以的，如果需要计算或是统计的数据，那么应该考虑用real类型存储。

### 第二步：创建实体对象

该对象即为一个行数据记录，实体属性与数据表对应，用于映射表中数据，此处创建为RateItem.java，代码如下

```
package com.zhouf.hello;

public class RateItem {
	private int id;
	private String curName;
	private String curRate;
	
	public RateItem() {
		super();
		curName = "";
		curRate = "";
	}
	public RateItem(String curName, String curRate) {
		super();
		this.curName = curName;
		this.curRate = curRate;
	}
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getCurName() {
		return curName;
	}
	public void setCurName(String curName) {
		this.curName = curName;
	}
	public String getCurRate() {
		return curRate;
	}
	public void setCurRate(String curRate) {
		this.curRate = curRate;
	}
}
```

此类定义了两个构造方法，一个为空参数，另一个为两个字符参数，主要考虑在用时方便一点。

### 第三步：创建与业务相关的操作类

与业务相关的操作如新增，修改，删除汇率数据等操作，应该属于与业务相关，此处我们需要保存所有汇率数据，删除之前所有历史数据，列出所有数据这三项功能，所以提供这几项功能就好了，为了说明一下其它操作，同时把修改，获取等方法也都一并实现了，供参考。业务操作相关类定义为DBManager.java，代码如下

```
package com.zhouf.hello;

import java.util.ArrayList;
import java.util.List;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;

public class DBManager {
	
	private DBHelper dbHelper;
	private String TBNAME;
	
	public DBManager(Context context) {
		dbHelper = new DBHelper(context);
		TBNAME = DBHelper.TB_NAME;
	}

	public void add(RateItem item){
		SQLiteDatabase db = dbHelper.getWritableDatabase();
		ContentValues values = new ContentValues();
		values.put("curname", item.getCurName());
		values.put("currate", item.getCurRate());
		db.insert(TBNAME, null, values);
		db.close();
	}
	
	public void addAll(List<RateItem> list){
		SQLiteDatabase db = dbHelper.getWritableDatabase();
		for (RateItem item : list) {
			ContentValues values = new ContentValues();
			values.put("curname", item.getCurName());
			values.put("currate", item.getCurRate());
			db.insert(TBNAME, null, values);
		}
		db.close();
	}
	
	public void deleteAll(){
		SQLiteDatabase db = dbHelper.getWritableDatabase();
		db.delete(TBNAME,null,null);
		db.close();
	}
	
	public void delete(int id){
		SQLiteDatabase db = dbHelper.getWritableDatabase();
		db.delete(TBNAME, "ID=?", new String[]{String.valueOf(id)});
		db.close();
	}

	public void update(RateItem item){
		SQLiteDatabase db = dbHelper.getWritableDatabase();
		ContentValues values = new ContentValues();
		values.put("curname", item.getCurName());
		values.put("currate", item.getCurRate());
		db.update(TBNAME, values, "ID=?", new String[]{String.valueOf(item.getId())});
		db.close();
	}
	
	public List<RateItem> listAll(){
		List<RateItem> rateList = null;
		SQLiteDatabase db = dbHelper.getReadableDatabase();
		Cursor cursor = db.query(TBNAME, null, null, null, null, null, null);
		if(cursor!=null){
			rateList = new ArrayList<RateItem>();
			while(cursor.moveToNext()){
				RateItem item = new RateItem();
				item.setId(cursor.getInt(cursor.getColumnIndex("ID")));
				item.setCurName(cursor.getString(cursor.getColumnIndex("CURNAME")));
				item.setCurRate(cursor.getString(cursor.getColumnIndex("CURRATE")));
				
				rateList.add(item);
			}
			cursor.close();
		}
		db.close();
		return rateList;
		
	}
	
	public RateItem findById(int id){
		SQLiteDatabase db = dbHelper.getReadableDatabase();
		Cursor cursor = db.query(TBNAME, null, "ID=?", new String[]{String.valueOf(id)}, null, null, null);
		RateItem rateItem = null;
		if(cursor!=null && cursor.moveToFirst()){
			rateItem = new RateItem();
			rateItem.setId(cursor.getInt(cursor.getColumnIndex("ID")));
			rateItem.setCurName(cursor.getString(cursor.getColumnIndex("CURNAME")));
			rateItem.setCurRate(cursor.getString(cursor.getColumnIndex("CURRATE")));
			cursor.close();
		}
		db.close();
		return rateItem;
	}
}
```

业务表中需要操作相对应的数据表，所以需要知道表名，此处获得DBHelper中的表名即可，如果有多个表需要操作，可以创建一个包，放入所有的业务操作类，每个表对应一个操作类。

注意这里的数据查找、修改、删除的方法和通常直接用SQL语句操作数据库还是有些不同的。在此程序中主要用到addAll、deleteAll、listAll三个方法，用于添加所有汇率，删除原有所有汇率数据，获取表中所保存的汇率数据。

### 第四步：修改列表页面

为了处理按日期更新数据，需要在程序中保存记录数据表中的数据日期，这里可以用一个数据表来存放 ，也可以用之前学到的SharedPreferences来保存日期数据，此处把日期数据放在SharedPreferences里，用于保存数据库中的汇率是哪一天的数据。我们对普通列表做修改，在RateListActivity中添加两个属性

```
private String logDate = "";
private final String DATE_SP_KEY = "lastRateDateStr";
```

第一个logDate用于保存从SP里获得的数据，DATE\_SP\_KEY用于记录保存在SP中的数据KEY。在onCreate方法里首先需要获取SP中的日期记录，放在super.onCreate\(…\)之后

```
super.onCreate(savedInstanceState);
// setContentView(R.layout.activity_rate_list);

SharedPreferences sp = getSharedPreferences("myrate", Context.MODE_PRIVATE);
logDate = sp.getString(DATE_SP_KEY, "");
Log.i("List","lastRateDateStr=" + logDate);
```

修改run方法，对获取当前日期，和logDate进行比对，以确定获取数据的方式，如果日期相等，从数据库中获取数据用于显示，如果不相等，说明存储数据已过期，重新获取在线数据，清理之前保留的数据，将新数据重新写入数据库，修改后的run方法代码如下

```
@Override
public void run() {
	Log.i("List","run...");
	List<String> retList = new ArrayList<String>();
	Message msg = handler.obtainMessage();
	String curDateStr = (new SimpleDateFormat("yyyy-MM-dd")).format(new Date());
	Log.i("run","curDateStr:" + curDateStr + " logDate:" + logDate);
	if(curDateStr.equals(logDate)){
		//如果相等，则不从网络中获取数据
		Log.i("run","日期相等，从数据库中获取数据");
		DBManager dbManager = new DBManager(RateListActivity.this);
		for(RateItem rateItem : dbManager.listAll()){
			retList.add(rateItem.getCurName() + "=>" + rateItem.getCurRate());
		}
	}else{
		Log.i("run","日期相等，从网络中获取在线数据");
		//获取网络数据
		try {
			List<RateItem> rateList = new ArrayList<RateItem>();
			URL url = new URL("http://www.usd-cny.com/bankofchina.htm");
			HttpURLConnection httpConn = (HttpURLConnection) url.openConnection();
			InputStream in = httpConn.getInputStream();
			String retStr = IOUtils.toString(in,"gb2312");
			
			//Log.i("WWW","retStr:" + retStr);
			//需要对获得的html字串进行解析，提取相应的汇率数据...
			
			Document doc = Jsoup.parse(retStr);
			Elements tables  = doc.getElementsByTag("table");
			
			Element retTable = tables.get(5);
			Elements tds = retTable.getElementsByTag("td");
			int tdSize = tds.size();
			for(int i=0;i<tdSize;i+=8){
				Element td1 = tds.get(i);
				Element td2 = tds.get(i+5);
				//Log.i("www","td:" + td1.text() + "->" + td2.text());
				float val = Float.parseFloat(td2.text());
				val = 100/val;
				retList.add(td1.text() + "->" + val);
				
				RateItem rateItem = new RateItem(td1.text(),td2.text());
				rateList.add(rateItem);
			}
			DBManager dbManager = new DBManager(RateListActivity.this);
			dbManager.deleteAll();
			Log.i("db","删除所有记录");
			dbManager.addAll(rateList);
			Log.i("db","添加新记录集");
			
		} catch (MalformedURLException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
		
		//更新记录日期
		SharedPreferences sp = getSharedPreferences("myrate", Context.MODE_PRIVATE);
		Editor edit = sp.edit();
		edit.putString(DATE_SP_KEY, curDateStr);
		edit.commit();
		Log.i("run","更新日期结束：" + curDateStr);
	}
	
	msg.obj = retList;
	msg.what = msgWhat;
	handler.sendMessage(msg);
}
```

至此，对获取数据方式的修改结束，运行验证。

### 扩展练习：

在程序中添加配置页面，可按指定时间频率更新汇率






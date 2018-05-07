# 自定义列表

> 实验目标：实现用户自定义列表的显示

### 实验步骤

第一步：创建列表页面

创建一个RateListActivity页面，继承于ListActivity，并添加如下三个属性

```
Handler handler;
private ArrayList<HashMap<String, String>> listItems; // 存放文字、图片信息
private SimpleAdapter listItemAdapter; // 适配器
private int msgWhat = 7;
```

listItems为一个列表集合，其中每个元素为一个Map对象，在Map对象中包含有用于列表显示的数据项，listItemAdapter适配器用于关联数据和布局文件。

第二步：实现静态数据的列表显示

添加一个布局文件list\_item.xml，用于处理每个列表元素的布局，即列表中每行的布局样式，详细信息如下

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/itemTitle" 
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:layout_width="fill_parent"/>
    <TextView
        android:id="@+id/itemDetail" 
        android:layout_height="wrap_content"
        android:textSize="15sp"
        android:layout_width="fill_parent"/>

</LinearLayout>
```

在布局文件里添加了两个TextView用于显示数据，并设置字体大小和位置不同，确定两个控件ID值分别为itemTitle和itemDetail

在RateListActivity2.java中创建一个方法用于初始化静态数据，此处方法名为initListView，代码如下

```
private void initListView() {
    listItems = new ArrayList<HashMap<String, String>>();
    for (int i = 0; i < 10; i++) {
        HashMap<String, String> map = new HashMap<String, String>();
        map.put("ItemTitle", "Rate： " + i); // 标题文字
        map.put("ItemDetail", "detail" + i); // 详情描述
        listItems.add(map);
    }
    // 生成适配器的Item和动态数组对应的元素
    listItemAdapter = new SimpleAdapter(this, listItems, // listItems数据源
            R.layout.list_item, // ListItem的XML布局实现
            new String[] { "ItemTitle", "ItemDetail" }, 
            new int[] { R.id.itemTitle, R.id.itemDetail } 
    );
}
```

在for循环中初始化map对象，并放于listItems集合中，准备需要显示的数据。完善适配器，关联数据和布局文件，以及确定map中的数据项和布局文件中的id绑定。

修改onCreate方法，注释掉

```
//setContentView(R.layout.activity_rate_list_activity2);
```

添加初始化方法和应用adapter

```
initListView();
this.setListAdapter(listItemAdapter);
```

运行程序，可见自定义列表的显示。

第三步：获取在线数据

实现Runnable接口，开启线程

```
Thread t = new Thread(this); // 创建新线程
t.start(); // 开启线程
```

在子线程中获取数据时，返回的应该是一个List对象，其中的元素为数据map，在此过程中，需要将每个币种数据封装成一个map对象，然后添加了list列表中带回，编码如下

```
public void run() {
    Log.i("thread","run.....");
    boolean marker = false;
    List<HashMap<String, String>> rateList = new ArrayList<HashMap<String, String>>();

    try {
            Document doc = Jsoup.connect("http://www.usd-cny.com/icbc.htm").get();
            Elements tbs = doc.getElementsByClass("tableDataTable");
            Element table = tbs.get(0);
            Elements tds = table.getElementsByTag("td");
            for (int i = 6; i < tds.size(); i+=6) {
                Element td = tds.get(i);
                Element td2 = tds.get(i+3);
                String tdStr = td.text();
                String pStr = td2.text();

                HashMap<String, String> map = new HashMap<String, String>();
                map.put("ItemTitle", tdStr);
                map.put("ItemDetail", pStr);

                rateList.add(map);
                Log.i("td",tdStr + "=>" + pStr);
            }
            marker = true;
        } catch (MalformedURLException e) {
            Log.e("www", e.toString());
            e.printStackTrace();
        } catch (IOException e) {
            Log.e("www", e.toString());
            e.printStackTrace();
        }

    Message msg = handler.obtainMessage();
    msg.what = msgWhat;
    if(marker){
        msg.arg1 = 1;
    }else{
        msg.arg1 = 0;
    }

    msg.obj = rateList;
    handler.sendMessage(msg);

    Log.i("thread","sendMessage.....");
}
```

可以用msg中的arg1属性标记操作成功与否

在主线程中实现消息处理，将获取到的数据用于adapter关联，并显示数据

```
public void handleMessage(Message msg) {
    if(msg.what == msgWhat){
        List<HashMap<String, String>> retList = (List<HashMap<String, String>>) msg.obj;
        SimpleAdapter adapter = new SimpleAdapter(RateListActivity.this, retList, // listItems数据源
                R.layout.list_item, // ListItem的XML布局实现
                new String[] { "ItemTitle", "ItemDetail" }, 
                new int[] { R.id.itemTitle, R.id.itemDetail });
        setListAdapter(adapter);
        Log.i("handler","reset list...");
    }
    super.handleMessage(msg);
}
```

至此，用于获取实时数据并显示的操作完成。

### 使用自定义Adapter实现

在实现自定义列表时，也可以使用自定义Adapter实现，过程如下。首先创建一个adapter继承ArrayAdapter

```
public class MyAdapter extends ArrayAdapter {

    private static final String TAG = "MyAdapter";

    public MyAdapter(Context context, int resource, ArrayList<HashMap<String,String>> list) {
        super(context, resource, list);
    }

    @NonNull
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View itemView = convertView;
        if(itemView == null){
            itemView = LayoutInflater.from(getContext()).inflate(R.layout.list_item,parent,false);
        }

        Map<String,String> map = (Map<String, String>) getItem(position);
        TextView title = (TextView) itemView.findViewById(R.id.itemTitle);
        TextView detail = (TextView) itemView.findViewById(R.id.itemDetail);

        title.setText("Title:" + map.get("ItemTitle"));
        detail.setText("detail:" + map.get("ItemDetail"));

        return itemView;
    }
}
```

通过构造方法传入数据list，此处为了兼容List数据，List里还是使用Map数据集合，最主要的方法就是getView，为列表提供显示所需要的视图，通过加载布局文件构造View并填充相应的数据，之后修改Activity页面，使用自定义的MyAdapter

```
MyAdapter myAdapter = new MyAdapter(this,R.layout.list_item,listItems);
this.setListAdapter(myAdapter);
```

通常情况自定义的Adapter中使用的数据类型为自定义实体List&lt;Object&gt;




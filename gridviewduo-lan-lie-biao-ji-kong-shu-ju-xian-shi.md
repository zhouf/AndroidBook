## GridView显示多栏列表

如果要显示多栏列表，可用GridView实现，在页面布局文件里把ListView替换成GridView

```
<GridView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:columnWidth="100dp"
        android:numColumns="3"
        android:id="@+id/mylist" />
```

添加了两个属性columnWidth和numColumns，columnWidth为指定最小宽度，numColumns为栏的个数，其值设置可以为`auto_fit`，当设置为`auto_fit`时,即为自动处理栏数，系统会根据屏幕宽度及columnWidth来自动设置应该显示几列，修改了布局文件后，代码也需要做出相应调整，将ListView修改为GridView

```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_my_list);

    GridView listView = (GridView) findViewById(R.id.mylist);
    //init data
    for(int i=0;i<100;i++){
        data.add("item" + i);
    }

    adapter = new ArrayAdapter<String>(this,android.R.layout.simple_list_item_1,data);
    listView.setAdapter(adapter);
    listView.setEmptyView(findViewById(R.id.nodata));
    listView.setOnItemClickListener(this);
}
```

显示效果如下

![](/assets/GridView.PNG)

## 空数据显示

当列表数据为空时，可设置列表显示一个视图如TextView，在`activity_my_list.xml`中添加一个TextView控件如下

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_my_list"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.swufe.hello.MyListActivity">

    <ListView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/mylist" />

    <TextView
        android:id="@+id/nodata"
        android:text="No Data"
        android:gravity="center"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

在onCreate方法中添加如下设置

```
listView.setEmptyView(findViewById(R.id.nodata));
```

可实现当列表没有数据时显示指定内容，如下图

![](/assets/nodata.PNG)

## 列表数据的删除

当列表adapter为ArrayAdapter时，可采用以下方式删除列表数据

```
adapter.remove(listview.getItemAtPosition(position));
```

当前案例以实现单击列表后删除数据，首先需要添加事件监听

```
listView.setOnItemClickListener(this);
```

在事件处理里添加删除语句，即

```
@Override
public void onItemClick(AdapterView<?> listview, View view, int position, long id) {
    Log.i(TAG, "onItemClick: position=" + position);
    Log.i(TAG, "onItemClick: parent" + listview);
    adapter.remove(listview.getItemAtPosition(position));
    //adapter.notifyDataSetChanged();
}
```

通过adapter删除数据时，notifyDataSetChanged\(\)会自动调用


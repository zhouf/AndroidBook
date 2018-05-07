

## 空数据显示

当列表数据为空时，可设置列表显示一个视图如TextView，在`activitymylist.xml中添加一个TextView控件如下`

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

当列表adapter为ArrayAdapter时，可采用以下方式删除列表数据，当前案例以实现单击列表后删除数据




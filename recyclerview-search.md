# SearchView的使用

> 实验目标
>
> 掌握RecyclerView列表数据的查找处理

RecyclerView部分的内容不再重复，主要介绍查询的功能实现。在RecyclerView中实现查询，需要修改Adapter适配器，添加过滤功能，再配合菜单项完成，具体实现过程见正文


## 如下内容参见RecyclerView
1. 创建Activity
2. 创建页面布局，加入RecyclerView控件
3. 准备实体数据类
4. 创建行布局文件
5. 准备Adapter及ViewHolder

此处对Adapter作了调整，由数组改为了List类型
```
private ArrayList<ItemData> listdata;

public MyRecyclerAdapter(ArrayList<ItemData> listdata) {
    this.listdata = listdata;
}

...

public void onBindViewHolder(@NonNull MyRecyclerAdapter.ViewHolder holder, int position) {
    final ItemData itemData = listdata.get(position);
    ...
}
```

在Activity中添加了初始数据的方法，此方法仅为测试准备数据集，在应用中数据集可由其它方式获得
```
private ArrayList<ItemData> initdata(){
    Random rand = new Random();
    ArrayList<ItemData> retlist = new ArrayList<ItemData>();
    for(int i=1;i<200;i++){
        retlist.add(new ItemData("Item - " + rand.nextInt(1000), android.R.drawable.ic_dialog_email));
    }
    return retlist;
}
```

在onCreate方法中直接调用
```
protected void onCreate(Bundle savedInstanceState) {

    ...
    recyclerView = findViewById(R.id.recyclerview);
    adapter = new MyRecyclerAdapter(initdata());
    recyclerView.setLayoutManager(new LinearLayoutManager(this));
    recyclerView.setAdapter(adapter);
}
```

初始状态准备完毕，下面加入查询功能

## 创建菜单资源文件

在res目录下创建menu类型资源目录，并在其中创建资源文件my_menu.xml，为了有更好的显示效果，可以先准备一个图标资源ic_search_24，菜单项配置内容如下
```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/action_search"
        android:icon="@drawable/ic_search_24"
        android:title="Search"
        app:showAsAction="always|collapseActionView"
        app:actionViewClass="androidx.appcompat.widget.SearchView" />
</menu>
```
为菜单项添加actionViewClass，指向androidx.appcompat.widget.SearchView

## 在Activity中启用菜单

重写onCreateOptionsMenu方法，加载my_menu.xml，并添加对ActionView的处理
```
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.my_menu,menu);
    MenuItem item = menu.findItem(R.id.action_search);
    SearchView searchView = (SearchView) item.getActionView();
    searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
        @Override
        public boolean onQueryTextSubmit(String query) {
            return false;
        }

        @Override
        public boolean onQueryTextChange(String newText) {
            return false;
        }
    });
    return super.onCreateOptionsMenu(menu);
}
```

## 修改Adapter

**1、添加属性**

为Adapter添加过滤功能，为保留原始所有数据项，添加了一个属性listdataAll
```
private ArrayList<ItemData> listdata;
private ArrayList<ItemData> listdataAll;

public MyRecyclerAdapter(ArrayList<ItemData> listdata) {
    this.listdata = listdata;
    this.listdataAll = new ArrayList<ItemData>(listdata);
}
```

**2、改写getFilter**

重写getFilter方法，返回Filter对象
```
@Override
public Filter getFilter() {
    return filter;
}
```

**3、定义过滤器**

上面需要返回一个过滤器，接下来需要定义一个过滤器对象，在Adapter类中实现，作为Adapter类的一个属性
```
Filter filter = new Filter() {

    //run as background thread
    @Override
    protected FilterResults performFiltering(CharSequence charSequence) {
        List<ItemData> filterList = new ArrayList<ItemData>();
        if(charSequence.toString().isEmpty()){
            filterList.addAll(listdataAll);
        }else{
            for(ItemData item : listdataAll){
                if(item.getDescription().contains(charSequence)){
                    filterList.add(item);
                }
            }
        }

        FilterResults results = new FilterResults();
        results.values = filterList;
        return results;
    }

    //run as ui thread
    @Override
    protected void publishResults(CharSequence charSequence, FilterResults filterResults) {
        listdata.clear();
        listdata.addAll((Collection<? extends ItemData>) filterResults.values);
        notifyDataSetChanged();
    }
};
```
其中performFiltering是属于后台运行的任务，查找的过滤规则主要在此方法中实现，publishResults是用于处理UI线程的内容，主要是重新构造数据，刷新列表

## 修改查询监听

修改SearchView中的查询处理，调用Adapter中的Filter进行数据项过滤
```
searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
    ...

    @Override
    public boolean onQueryTextChange(String newText) {
        adapter.getFilter().filter(newText);
        return false;
    }
});
```

需要在Activity中把adapter对象提升为类属性，以便在方法中调用
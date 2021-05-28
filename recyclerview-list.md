# RecyclerView控件使用

> 实验目标
>
> 掌握RecyclerView列表控件显示

RecyclerView是谷歌公司推出了一个用于大量数据展示的新控件，可以用来代替传统的ListView，更加强大和灵活。


## 引入RecyclerView
在app的build.gradle文件中添加引用，目前使用androidx包
```
dependencies {
    ...
    implementation 'androidx.recyclerview:recyclerview:1.2.0'
}
```

## 页面布局
页面MainActivity中放入一个RecyclerView控件，布局文件如下
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    tools:context=".MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

## 数据实体
准备数据实体对象，定义一个类，用于存放每行中的数据，此处定义为ItemData，其中包含两个属性，分别为描述文本及图片资源id，参考代码如下
```
public class ItemData {
    private String description;
    private int imgId;
    public ItemData(String description, int imgId) {
        this.description = description;
        this.imgId = imgId;
    }
    public String getDescription() {
        return description;
    }
    public void setDescription(String description) {
        this.description = description;
    }
    public int getImgId() {
        return imgId;
    }
    public void setImgId(int imgId) {
        this.imgId = imgId;
    }
}
```

## 行布局
准备每行的布局文件list_item.xml，放入一个图片控件入一个文本控件，参考如下
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/relativeLayout"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeightLarge"
    android:background="@drawable/border">

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerVertical="true"
        android:layout_alignParentStart="true"
        android:layout_alignParentLeft="true"
        android:layout_marginStart="16dp"
        android:layout_marginEnd="16dp"
        android:contentDescription="Icon" />

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_toEndOf="@id/imageView"
        android:layout_toRightOf="@id/imageView"
        android:gravity="center_vertical"
        android:textSize="16sp"/>

</RelativeLayout>
```
此处采用RalativeLayout布局，也可采用ConstraintLayout实现对行元素的布局设置

## 准备Adapter及ViewHolder

**ViewHolder**

ViewHolder处理每一个列表项视图，其中定义每行的各控件，对行布局文件进行解析。其父类应为RecyclerView.ViewHolder，此处把ViewHoder的定义放在Adapter类内部，参考代码如下
```
public static class ViewHolder extends RecyclerView.ViewHolder {
    public ImageView imageView;
    public TextView textView;
    public RelativeLayout relativeLayout;
    public ViewHolder(View itemView) {
        super(itemView);
        this.imageView = (ImageView) itemView.findViewById(R.id.imageView);
        this.textView = (TextView) itemView.findViewById(R.id.textView);
        this.relativeLayout = (RelativeLayout)itemView.findViewById(R.id.relativeLayout);
    }
}
```

**RecyclerView.Adapter**

Adapter的作用主要是对数据和控件进行关联，在RecyclerView中使用的Adapter为RecyclerView.Adapter，通常需要对其子类进行修改，此处创建类MyRecyclerAdapter，继承父类，添加ViewHoder类型，在构造函数中接收数据项，参考代码如下
```
public class MyRecyclerAdapter extends RecyclerView.Adapter<MyRecyclerAdapter.ViewHolder>{
    private ItemData[] listdata;

    public MyRecyclerAdapter(ItemData[] listdata) {
        this.listdata = listdata;
    }

    //ViewHolder类定义
    public static class ViewHolder extends RecyclerView.ViewHolder {
        ...
    }
}
```

继承父类后，重写三个方法

**1、onCreateViewHolder(ViewGroup parent, int viewType)**

当需要创建新的ViewHolder来显示列表项时，Adapter会自动调用onCreateViewHolder方法去创建ViewHolder
```
public MyRecyclerAdapter.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
    LayoutInflater layoutInflater = LayoutInflater.from(parent.getContext());
    View listItem= layoutInflater.inflate(R.layout.list_item, parent, false);
    ViewHolder viewHolder = new ViewHolder(listItem);
    return viewHolder;
}
```

**2、onBindViewHolder(MyRecyclerAdapter.ViewHolder holder, int position)**

此方法完成数据绑定，将数据项和ViewHolder进行关联
```
@Override
public void onBindViewHolder(@NonNull MyRecyclerAdapter.ViewHolder holder, int position) {
    final ItemData itemData = listdata[position];
    holder.textView.setText(itemData.getInfo());
    holder.imageView.setImageResource(itemData.getPicId());
    holder.relativeLayout.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            Toast.makeText(view.getContext(),"Click: "+ itemData.getInfo(),Toast.LENGTH_SHORT).show();
        }
    });
}
```
上述方法处理了对行的单击事件


**3、getItemCount()**

返回数据个数，对应列表需要显示多少行数据
```
@Override
public int getItemCount() {
    return listdata.length;
}
```

## 页面加载

准备数据项，使用系统自带的图片资源
```
ItemData[] itemData = new ItemData[] {
    new ItemData("Email", android.R.drawable.ic_dialog_email),
    new ItemData("Info", android.R.drawable.ic_dialog_info),
    new ItemData("Delete", android.R.drawable.ic_delete),
    new ItemData("Dialer", android.R.drawable.ic_dialog_dialer),
    new ItemData("Alert", android.R.drawable.ic_dialog_alert),
    new ItemData("Map", android.R.drawable.ic_dialog_map),
    new ItemData("Email", android.R.drawable.ic_dialog_email),
    new ItemData("Info", android.R.drawable.ic_dialog_info),
    new ItemData("Delete", android.R.drawable.ic_delete),
    new ItemData("Dialer", android.R.drawable.ic_dialog_dialer),
    new ItemData("Alert", android.R.drawable.ic_dialog_alert),
    new ItemData("Map", android.R.drawable.ic_dialog_map),
};
```
获取控件，参数设置，初始化Adapter，绑定Adapter
```
RecyclerView recyclerView = findViewById(R.id.recyclerview);
MyRecyclerAdapter adapter = new MyRecyclerAdapter(itemData);
recyclerView.setLayoutManager(new LinearLayoutManager(this));
recyclerView.setAdapter(adapter);
```

对RecyclerView进行设置时，LayoutManager和Adapter是必须要进行设置的，其它项可以选择设置参数

如果将LayoutManager设置为
```
recyclerView.setLayoutManager(new LinearLayoutManager(this,LinearLayoutManager.HORIZONTAL,false));
```
则可实现水平滑动，第三个参数为是否逆转顺序，如果为true，则表示水平方向起始位置在最左端，垂直方向起始位置在最下端。如果要实现3列网络布局，可设置LayoutManager为
```
recyclerView.setLayoutManager(new GridLayoutManager(this,3));
```
其它参数效果可自行尝试


---


## 附：
RecyclerView提供了三种布局管理器：

- LinerLayoutManager 以垂直或者水平列表方式展示数据项
- GridLayoutManager 以网格方式展示数据项
- StaggeredGridLayoutManager 以瀑布流方式展示数据项

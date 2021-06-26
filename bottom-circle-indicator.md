## 小圆点切换提示

通常在应用的起始页面都会有一些向导页面，可以使用ViewPager2组件以及第三方工具来实现。此例使用数据生成5个页面，并使用ViewPager2进行切换显示，步骤参考如下


### 添加第三方库

修改文件`app/build.gradle`，添加`circleindicator`组件

```
dependencies {

    //...
    implementation 'me.relex:circleindicator:2.1.6'
}
```
编写此文档时，组件的版本为2.1.6

### 修改Activity页面布局

在页面布局中添加`ViewPager2`和`CircleIndicator3`控件，并设置好位置
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <me.relex.circleindicator.CircleIndicator3
        android:id="@+id/indicator"
        android:layout_width="match_parent"
        android:layout_height="35dp"
        android:background="#27424242"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/view_pager2"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toTopOf="@+id/indicator"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### 准备ViewPager2内布局

在`res/layout`目录中创建`item_page.xml`布局文件，用于描述切换内容的布局，此例放置两个文本控件及一个图片控件
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/tvImage"
        android:layout_marginTop="75dp"
        android:elevation="10dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_width="120dp"
        android:layout_height="120dp"
        android:clickable="true"
        android:focusable="true"
        app:srcCompat="@mipmap/ic_launcher" />

    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="28dp"
        android:text="Title Text"
        android:textSize="32sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/tvImage" />

    <TextView
        android:id="@+id/tvAbout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="About Topic"
        android:textSize="24sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/tvTitle" />

</androidx.constraintlayout.widget.ConstraintLayout>
```


### 准备Adapter适配器

创建JAVA类`ViewPagerAdapter`，接收三个List参数传入，构造显示对象，代码参考如下

```
public class ViewPagerAdapter extends RecyclerView.Adapter<ViewPagerAdapter.Pager2ViewHolder> {
    private List<String> titles;
    private List<String> details;
    private List<Integer> images;

    public ViewPagerAdapter(List<String> titles, List<String> details, List<Integer> images) {
        this.titles = titles;
        this.details = details;
        this.images = images;
    }

    @NonNull
    @Override
    public Pager2ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        return new Pager2ViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout.item_page, parent, false));
    }

    @Override
    public void onBindViewHolder(@NonNull ViewPagerAdapter.Pager2ViewHolder holder, int position) {
        holder.itemTitle.setText(titles.get(position));
        holder.itemDetail.setText(details.get(position));
        holder.itemImage.setImageResource(images.get(position));
    }

    @Override
    public int getItemCount() {
        return titles.size();
    }

    public class Pager2ViewHolder extends RecyclerView.ViewHolder {
        public TextView itemTitle;
        public TextView itemDetail;
        public ImageView itemImage;
        public Pager2ViewHolder(@NonNull View itemView) {
            super(itemView);
            itemTitle = itemView.findViewById(R.id.tvTitle);
            itemDetail = itemView.findViewById(R.id.tvAbout);
            itemImage = itemView.findViewById(R.id.tvImage);
        }
    }
}
```

### 在页面中调用

修改Activity页面代码，加载控件，调用对应方法
```
public class MainActivity extends AppCompatActivity {

    private List<String> titlesList = new ArrayList<String>();
    private List<String> detailsList = new ArrayList<String>();
    private List<Integer> imagesList = new ArrayList<Integer>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        postToList();

        ViewPager2 viewPager2 = findViewById(R.id.view_pager2);
        viewPager2.setAdapter(new ViewPagerAdapter(titlesList,detailsList,imagesList));
        viewPager2.setOrientation(ViewPager2.ORIENTATION_HORIZONTAL);

        CircleIndicator3 indicator = findViewById(R.id.indicator);
        indicator.setViewPager(viewPager2);
    }

    private void postToList(){
        for(int i=1;i<=5;i++){
            titlesList.add("Title " + i);
            detailsList.add("Detail " + i);
            imagesList.add(R.mipmap.ic_launcher);
        }
    }
}
```


## 补充内容

### 更换矢量图

可选用系统中的矢量图，在工程视图下将`res/drawable-v24`目录中的`ic_launcher_foreground.xml`文件移动到`res/drawable`目录中

在ImageView中使用矢量图需要进行配置，修改`app/build.gradle`文件，在`android.defaultConfig`下添加配置`vectorDrawables.useSupportLibrary = true`
```
android {
   
    //...

    defaultConfig {

        //...

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
        vectorDrawables.useSupportLibrary = true
    }
```
将`item_page.xml`布局文件中的图片属性换成`@drawable/`中的资源
```
<ImageView
    android:id="@+id/tvImage"
    android:layout_marginTop="75dp"
    android:elevation="10dp"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    android:layout_width="120dp"
    android:layout_height="120dp"
    android:clickable="true"
    android:focusable="true"
    android:background="@drawable/ic_launcher_background"
    app:srcCompat="@drawable/ic_launcher_foreground" />
```
修改图片加载为`R.drawable.ic_launcher_foreground`
```
private void postToList(){
    for(int i=1;i<=5;i++){
        titlesList.add("Title " + i);
        detailsList.add("Detail " + i);
        imagesList.add(R.drawable.ic_launcher_foreground);
    }
}
```

### 去掉页面标题行

修改`AndroidManifest.xml`文件，将`activity`主题改为
```
<activity android:name=".MainActivity" android:theme="@style/Theme.AppCompat.Light.NoActionBar">
```

### 使用圆形图片

将图片改为圆形，可在`ImageView`外嵌套`CardView`，再嵌套`LinearLayout`，修改后的`item_page.xml`代码如下
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:id="@+id/llayout"
        android:layout_marginTop="75dp"
        android:elevation="10dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_width="120dp"
        android:layout_height="120dp">

    <androidx.cardview.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:elevation="10dp"
        app:cardCornerRadius="60dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <ImageView
            android:id="@+id/tvImage"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="@drawable/ic_launcher_background"
            android:clickable="true"
            android:focusable="true"
            android:scaleType="centerCrop"
            app:srcCompat="@drawable/ic_launcher_foreground" />
    </androidx.cardview.widget.CardView>

    </LinearLayout>
    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="28dp"
        android:text="Title Text"
        android:textSize="32sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/llayout" />

    <TextView
        android:id="@+id/tvAbout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="About Topic"
        android:textSize="24sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/tvTitle" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### 添加图片单击响应

修改`Pager2ViewHolder`中的方法，添加图片事件处理

```
public class Pager2ViewHolder extends RecyclerView.ViewHolder {
    public TextView itemTitle;
    public TextView itemDetail;
    public ImageView itemImage;
    public Pager2ViewHolder(@NonNull View itemView) {
        super(itemView);
        itemTitle = itemView.findViewById(R.id.tvTitle);
        itemDetail = itemView.findViewById(R.id.tvAbout);
        itemImage = itemView.findViewById(R.id.tvImage);
        itemImage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(itemView.getContext(), "Clicked item " + getAdapterPosition() + 1, Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```
# ViewPager2实现页面切换

> 目标：
>
> 学习掌握ViewPager2组件的基本用法，掌握TabLayout的使用

### 第一步：创建主页面布局
创建一个工程，修改其布局文件，加入ViewPager2控件
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager2"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### 第二步 准备三个切换页面
用最简的方式创建三个Fragment页面，如下创建FirstFragment，布局如下
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".FirstFragment">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/text1"
        android:gravity="center"
        android:text="Page1" />

</FrameLayout>
```
对应的java代码参考如下
```
public class FirstFragment extends Fragment {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.fragment_first, container, false);
    }
}
```
参照创建SecondFragment以及ThirdFragment

### 第三步 创建Adapter
Adapter用于处理ViewPager和各Fragment之间的协调，向ViewPager2提供显示的Fragment对象，创建类MyPageAdapter继承于FragmentStateAdapter代码如下
```
public class MyPageAdapter extends FragmentStateAdapter {

    public MyPageAdapter(@NonNull FragmentActivity fa) {
        super(fa);
    }

    @NonNull
    @Override
    public Fragment createFragment(int position) {
        if(position==0){
            return new FirstFragment();
        }else if(position==1){
            return new SecondFragment();
        }else{
            return new ThirdFragment();
        }
    }

    @Override
    public int getItemCount() {
        return 3;
    }
}
```

### 第四步 页面装配
在主页面Activity中初始化ViewPager对象，以及Adapter对象，然后进行绑定，在`onCreate()`方法中添加如下代码
```
ViewPager2 viewPager = findViewById(R.id.viewPage2);
MyPageAdapter pageAdapter = new MyPageAdapter(this);
viewPager.setAdapter(pageAdapter);
```
此时，可测试运行程序，查看页面切换效果

### 第五步 添加TabLayou控件
TabLayout控件可以显示当前ViewPager2的切换状态，也可以控制ViewPager2进行页面切换，要在程序中启用TabLayout控件需修改如下配置

添加控件TabLayout，添加完成后布局代码如下
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tab_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPage2"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintBottom_toBottomOf="@id/tab_layout"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```


在`onCreate()`方法中添加控件访问，TabLayout对象和ViewPager2对象的绑定关系是通过TabLayoutMediator对象来实现的，参考如下
```
TabLayout tabLayout = findViewById(R.id.tab_layout);
new TabLayoutMediator(tabLayout, viewPager,
        (tab, position) -> tab.setText("OBJECT " + (position + 1))
).attach();
```

### 其它配置

- 如果需要把TabLayout放在页面下端，可以通过页面布局进行调整
- 修改ViewPager2的滑动方向，可使用android:orientation="vertical"改为垂直方向滑动


### 扩展练习：

参考[官方文档](https://developer.android.google.cn/reference/kotlin/androidx/viewpager/widget/ViewPager.html?hl=en)，了解ViewPager和PagerTabStrip的使用

查阅网络资料，使用小圆点进行ViewPager切换显示


附：[Androidx 中的 ViewPager 与 ViewPager2](https://www.jianshu.com/p/924046eae137)

[从 ViewPager 迁移到 ViewPager2](https://developer.android.google.cn/training/animation/vp2-migration?hl=zh_cn)


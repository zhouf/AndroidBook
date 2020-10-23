# ViewPager实现页面切换

> 目标：
>
> 学习掌握ViewPager组件的基本用法，掌握TabLayout的使用

### 第一步：创建主页面布局
创建一个工程，修改其布局文件，加入ViewPager控件
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.a105.mypage.MainActivity">

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>
```

### 第二步 准备三个切换页面
用最简的方式创建三个Fragment页面，如下创建FirstFragment，布局如下
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.a105.mypage.FirstFragment">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:text="@string/label_first"
        android:gravity="center"
        android:id="@+id/textView" />

</FrameLayout>
```
生成的java代码不用修改，参照创建SecondFragment以及ThirdFragment

### 第三步 创建PageAdapter
PageAdapter用于处理ViewPager和各Fragment之间的协调，向ViewPager提供显示的Fragment对象，创建一类MyPageAdapter继承于FragmentPageAdapter代码如下
```
public class MyPageAdapter extends FragmentPagerAdapter {

    public MyPageAdapter(FragmentManager fm){
        super(fm);
    }
    
    @Override
    public Fragment getItem(int position) {
        if(position==0){
            return new FirstFragment();
        }else if(position==1){
            return new SecondFragment();
        }else{
            return new ThirdFragment();
        }
    }
    
    @Override
    public int getCount() {
        return 3;
    }
}
```

### 第四步 页面装配
在主页面Activity中初始化ViewPager对象，以及PageAdapter对象，然后进行绑定，在`onCreate()`方法中添加如下代码
```
ViewPager viewPager = (ViewPager) findViewById(R.id.viewpager);
MyPageAdapter pageAdapter = new MyPageAdapter(getSupportFragmentManager());
viewPager.setAdapter(pageAdapter);
```
此时，可测试运行程序，查看页面切换效果

### 第五步 添加TabLayou控件
TabLayout控件可以显示当前ViewPager的切换状态，也可以控制ViewPager进行页面切换，要在程序中启用TabLayout控件需修改如下配置

修改主页面布局，添加xmlns命名空间
```
xmlns:app="http://schemas.android.com/apk/res-auto"
```
添加控件TabLayout，添加完成后布局代码如下
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.a105.mypage.MainActivity">

    <android.support.design.widget.TabLayout
        android:id="@+id/sliding_tabs"
        style="@style/MyCustomTabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabMode="fixed" />

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>
```
在build.gradle中添加引用库
```
implementation 'com.android.support:design:25.3.1'
```
> 注意事项
>
> design库也会不断出新版本，需要注意与当前sdk版本相匹配，可以通过 https://mvnrepository.com/artifact/com.android.support/design?repo=google 查询

在`onCreate()`方法中添加控件访问，并绑定TabLayout对象和ViewPager对象
```
TabLayout tabLayout = (TabLayout) findViewById(R.id.sliding_tabs);
tabLayout.setupWithViewPager(viewPager);
```
如果ViewPager对象启用了TabLayout，需要PageAdapter提供显示标题的文本，修改MyPageAdapter，添加`getPageTitle()`方法
```
@Override
public CharSequence getPageTitle(int position) {
    return "Title" + position;
}
```
此处直接用Title加上位置返回，实际应用中可以根据position确定返回的字符串

### AndroidX

> 按照[官方文档](https://developer.android.google.cn/jetpack/androidx?hl=en)说明 AndroidX 是对 android.support.xxx 包的整理后产物。由于之前的 support 包过于混乱，所以，Google 推出了AndroidX。
> 在其后续版本中，会逐步放弃对 support 的升级和维护

如果工程使用的是androidx库，可引入下列包
```
implementation 'org.xutils:xutils:3.5.0'
implementation 'com.androidkun:XTabLayout:1.1.4'
```

也可直接使用androidx库中的ViewPager和PagerTabStrip组件来完成类似的效果


### 关于android.support.design.widget.TabLayout无法使用

在Android Studio3.5以后，可能无法使用android.support.design.widget.TabLayout，可选择com.google.android.material.tabs.TabLayout代替，如果是xml文件报错，需要确Activity和xml中的两个包一致，如果Activity中引入的是Google的，在xml中也选用Google的tablayout

配置文件中引入：
```
implementation 'com.android.support:support-v4:29.0.2'
implementation 'com.android.support:design:29.0.2'
```
activity_main.xml中部分代码
```
<com.google.android.material.tabs.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:background="#ffffff"
        app:tabIndicatorColor="@color/design_default_color_primary"
        app:tabIndicatorHeight="8dp"

        app:tabSelectedTextColor="#000000"
        app:tabMode="fixed"
        app:tabBackground="@drawable/selected"
        />
```
其余使用与android.support.design.widget.TabLayout相同

如果使用的是AndroidX库，引用的com.android.support:design:29.0.2 可以替换成com.google.android.material:material:1.0.0

详见：[Android AndroidX的迁移](https://www.jianshu.com/p/7dc111353328)


### 扩展练习：

参考[官方文档](https://developer.android.google.cn/reference/kotlin/androidx/viewpager/widget/ViewPager.html?hl=en)，了解ViewPager和PagerTabStrip的使用

查阅网络资料，使用小圆点进行ViewPager切换显示


附：[Androidx 中的 ViewPager 与 ViewPager2](https://www.jianshu.com/p/924046eae137)

[从 ViewPager 迁移到 ViewPager2](https://developer.android.google.cn/training/animation/vp2-migration?hl=zh_cn)


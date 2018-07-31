# 完成一个生日贺卡

> 实验目的
>
> 熟悉AndroidStudio界面操作，熟悉布局文件的使用，掌握多语言版本的设置

### 实验步骤

用向导创建一个应用，选择空布局Activity模板，生成项目结构。查找图片资源并引入到项目中，此处命名为bg.jpg，并放于mipmap中

修改activity\_main.xm布局文件，使用如下布局内容

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.swufe.hello.MainActivity">

    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:srcCompat="@mipmap/bg"
        android:layout_alignParentTop="true"
        android:layout_alignParentLeft="true"
        android:layout_alignParentStart="true"
        android:id="@+id/imageView"
        android:scaleType="centerCrop" />

    <TextView
        android:layout_width="wrap_content"
        android:text="@string/hello"
        android:textSize="25sp"
        android:gravity="center"
        android:textColor="@android:color/white"
        android:id="@+id/textView"
        android:layout_height="wrap_content"
        android:layout_marginTop="62dp"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true" />

    <TextView
        android:text="@string/from"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="30sp"
        android:textColor="@android:color/white"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:layout_alignParentEnd="true"
        android:layout_marginRight="24dp"
        android:layout_marginEnd="30dp"
        android:layout_marginBottom="20dp"
        android:id="@+id/textView3" />


</RelativeLayout>
```
在string.xml中添加文本资源如下
```
<resources>
    <string name="app_name">Hello</string>
    <string name="hello">Happy Birthday!</string>
    <string name="from">Tom</string>
</resources>
```
同时增加中文文本资源
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">嘿</string>
    <string name="hello">生日快乐！</string>
    <string name="from">汤姆</string>
</resources>
```
运行并测试多语言版本的显示效果


### 扩展练习

学习使用ConstraintLayout布局，并练习使用ConstraintLayout完成此页面


### 参考材料
ConstraintLayout相关文档
https://www.jianshu.com/p/792d2682c538
https://www.jianshu.com/p/a8b49ff64cd3

http://blog.csdn.net/guolin_blog/article/details/53122387

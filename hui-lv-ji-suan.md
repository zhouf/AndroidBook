# 完成一个汇率计算

> 实验目的
>
> 熟悉事件处理及页面布局，实现数据的计算

### 实验步骤

使用Empty Activity模板创建一个Activity,此处命名为RateActivity，完成页面布局如下
![](/assets/RateActivity.png)

配置好的activity_rate.xml如下所示
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_rate"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.swufe.hello.RateActivity">

    <EditText
        android:id="@+id/rmb"
        android:inputType="numberDecimal"
        android:layout_width="match_parent"
        android:gravity="center"
        android:layout_height="wrap_content"
        android:hint="@string/rmb_hint_label" />

    <TextView
        android:id="@+id/show"
        android:gravity="center"
        android:textSize="35sp"
        android:layout_width="match_parent"
        android:text="@string/app_name"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/dollar"
        android:onClick="onClick"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/btn_dollar_label"/>
    <Button
        android:id="@+id/euro"
        android:text="@string/btn_euro_label"
        android:onClick="onClick"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/won"
        android:onClick="onClick"
        android:text="@string/btn_won_label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
添加相应的文本资源
```
<string name="btn_label">Button</string>
<string name="btn_dollar_label">Dollar</string>
<string name="btn_euro_label">Euro</string>
<string name="btn_won_label">Won</string>
<string name="rmb_hint_label">Input RMB</string>
```
在RateActivity.java中先定义几个类变量，用于保存日志tag及三个币种汇率
```
private final String TAG = "Rate";
private float dollarRate = 0.1f;
private float euroRate = 0.2f;
private float wonRate = 0.3f;
```
以及用于标记控件的两个变量
```
EditText rmb;
TextView show;
```
在onCreate方法中获取对控件的引用
```
setContentView(R.layout.activity_rate);
rmb = (EditText) findViewById(R.id.rmb);
show = (TextView) findViewById(R.id.show);
```
完成对按钮的事件处理，因为三个按钮都由一个方法处理，需要在方法中区分事件来源，可以通过btn.getId()来进行判断。首先获取用户输入，然后根据不同币种计算出不同的结果，如果用户没有输入内容，则给出提示
```
Log.i(TAG, "onClick: ");
String str = rmb.getText().toString();
Log.i(TAG, "onClick: get str=" + str);

float r = 0;
if(str.length()>0){
    r = Float.parseFloat(str);
}else{
    //用户没有输入内容
    Toast.makeText(this, "请输入内容", Toast.LENGTH_SHORT).show();
    return;
}

Log.i(TAG, "onClick: r=" + r);
```
如果通过检查，则可以进行计算输出
```
//计算
if(btn.getId()==R.id.dollar){
    show.setText(String.valueOf(r*dollarRate));
}else if(btn.getId()==R.id.euro){
    show.setText(String.valueOf(r*euroRate));
}else{
    show.setText(String.valueOf(r*wonRate));
}
```
完成程序部分，运行并测试


### 扩展练习
格式化显示输出，保留两位小数


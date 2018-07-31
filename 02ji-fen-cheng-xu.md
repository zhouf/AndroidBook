# 完成一个计分程序

> 实验目的
>
> 熟悉界面布局及事件处理

### 实验步骤

创建一个新的Activity，命名为SecondActivity，修改布局文件以完成如下效果

![](/assets/teama.png)

添加字符串资源及绑定事件处理，详细布局文件如下
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_second"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.zhouf.leo.hello.SecondActivity">


    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        android:padding="@dimen/activity_horizontal_margin"
        android:layout_centerHorizontal="true">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/team_name"
            android:textSize="20sp"
            android:gravity="center"
            android:text="@string/tname_label" />

        <TextView
            android:text="0"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textSize="55sp"
            android:gravity="center"
            android:layout_marginTop="24dp"
            android:layout_marginBottom="24dp"
            android:id="@+id/score" />

        <Button
            android:text="@string/btn3_label"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="16dp"
            android:onClick="btn3"
            android:id="@+id/btn3" />

        <Button
            android:text="@string/btn2_label"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="16dp"
            android:onClick="btn2"
            android:id="@+id/btn2" />

        <Button
            android:text="@string/btn1_label"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="16dp"
            android:onClick="btn1"
            android:id="@+id/btn1" />
    </LinearLayout>

    <Button
        android:text="@string/btn_reset_label"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/reset"
        android:onClick="btnReset"
        android:layout_marginBottom="15dp"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true" />
</RelativeLayout>
```
在string.xml中加入相应的字符串资源文件
```
<string name="btn_reset_label">Reset</string>
<string name="btn3_label">+3 points</string>
<string name="btn2_label">+2 points</string>
<string name="btn1_label">+1 point</string>
<string name="tname_label">TeamA</string>
```
修改java代码，完成事件处理，实现功能，修改后的代码如下
```
public class SecondActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
    }


    public void btn1(View v) {
        show(1);
    }

    public void btn2(View v) {
        show(2);
    }

    public void btn3(View v) {
        show(3);
    }

    public void btnReset(View v) {
        TextView out = (TextView)findViewById(R.id.score);
        out.setText("0");
    }

    private void show(int i){
        TextView out = (TextView)findViewById(R.id.score);
        String oldScore = (String) out.getText();
        String newScore = String.valueOf(Integer.parseInt(oldScore) + i);
        out.setText(newScore);
    }
}
```
通过AndroidManifest.xml文件修改启动界面为SecondActivity，修改结果如下
```
<activity android:name=".MainActivity">
    <!--<intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>-->
</activity>
<activity android:name=".SecondActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```
启动程序，调试运行，验证程序功能


### 扩展练习

尝试修改此程序，实现两队的计分

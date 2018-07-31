# 完成汇率配置页面

> 实验目的
>
> 掌握窗口打开方法，页面参数传递及数据返回处理

### 实验步骤

使用Empty Activity模板创建一个新的Activity，命名为ConfigActivity，其页面布局需要有三个不同币种的汇率显示与输入，此外设定为三个EditText控件，设计页面布局如下  
![](/assets/RateConfig.png)  
详细布局文件如下所示

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_config"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.swufe.hello.ConfigActivity">


    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:id="@+id/dollar_rate"
        android:inputType="numberDecimal" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="numberDecimal"
        android:ems="10"
        android:id="@+id/euro_rate" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="numberDecimal"
        android:ems="10"
        android:id="@+id/won_rate" />

    <Button
        android:text="@string/btn_save_label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="save"
        android:id="@+id/btn_save" />
</LinearLayout>
```

修改activity\_rate.xml布局文件，添加一个按钮用于打开配置页面

```
<Button
    android:text="@string/btn_opencfg_label"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="24dp"
    android:onClick="openOne"
    android:id="@+id/btn_opencfg" />
```

在RateActivity中添加按钮事件处理方法openOne，用于传递汇率到下个页面显示，并打开配置页面

```
public void openOne(View btn){
    Intent config = new Intent(this,ConfigActivity.class);
    config.putExtra("dollar_rate_key",dollarRate);
    config.putExtra("euro_rate_key",euroRate);
    config.putExtra("won_rate_key",wonRate);

    Log.i(TAG, "openOne: dollarRate=" + dollarRate);
    Log.i(TAG, "openOne: euroRate=" + euroRate);
    Log.i(TAG, "openOne: wonRate=" + wonRate);

    //startActivity(config);
    startActivityForResult(config,1);
}
```

在ConfigActivity中定义TAG及EditText对象

```
private final String TAG = "ConfigActivity";

EditText dollarText;
EditText euroText;
EditText wonText;
```

在onCreate方法中接收传入的数据

```
setContentView(R.layout.activity_config);
Intent intent = getIntent();
float dollar2 = intent.getFloatExtra("dollar_rate_key",0.0f);
float euro2 = intent.getFloatExtra("euro_rate_key",0.0f);
float won2 = intent.getFloatExtra("won_rate_key",0.0f);

Log.i(TAG, "onCreate: dollar2=" + dollar2);
Log.i(TAG, "onCreate: euro2=" + euro2);
Log.i(TAG, "onCreate: won2=" + won2);
```

获取控件并显示出接收的参数值

```
dollarText = (EditText)findViewById(R.id.dollar_rate);
euroText = (EditText)findViewById(R.id.euro_rate);
wonText = (EditText)findViewById(R.id.won_rate);

//显示数据到控件
dollarText.setText(String.valueOf(dollar2));
euroText.setText(String.valueOf(euro2));
wonText.setText(String.valueOf(won2));
```

因为setText方法只接收文本类型参数，注意需要转换数据类型  
实现在布局文件中配置好的按钮事件处理save方法，用于处理保存汇率操作，首先需要获取控件新值

```
Log.i(TAG, "save: ");
//获取新的值
float newDollar = Float.parseFloat(dollarText.getText().toString());
float newEuroi = Float.parseFloat(euroText.getText().toString());
float newWon = Float.parseFloat(wonText.getText().toString());

Log.i(TAG, "save: 获取到新的值");
Log.i(TAG, "save: newDollar=" + newDollar);
Log.i(TAG, "save: newEuroi=" + newEuroi);
Log.i(TAG, "save: newWon=" + newWon);
```

通过intent对象向调用页面返回数据，此处用Bundle对象带回

```
//保存到Bundle或放入到Extra
Intent intent = getIntent();
Bundle bdl = new Bundle();
bdl.putFloat("key_dollar",newDollar);
bdl.putFloat("key_euro",newEuroi);
bdl.putFloat("key_won",newWon);
intent.putExtras(bdl);
setResult(2,intent);

//返回到调用页面
finish();
```

在RateActivity中实现onActivityResult方法用于处理返回数据

```
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if(requestCode==1 && resultCode==2){

        Bundle bundle = data.getExtras();
        dollarRate = bundle.getFloat("key_dollar",0.1f);
        euroRate = bundle.getFloat("key_euro",0.1f);
        wonRate = bundle.getFloat("key_won",0.1f);
        Log.i(TAG, "onActivityResult: dollarRate=" + dollarRate);
        Log.i(TAG, "onActivityResult: euroRate=" + euroRate);
        Log.i(TAG, "onActivityResult: wonRate=" + wonRate);
    }

    super.onActivityResult(requestCode, resultCode, data);
}
```

此处直接将从bundle中获取的数据赋值给dollarRate等二个类变量，可直接用于新的计算值。至此，页面功能实现完毕，运行并测试

### 扩展练习

为页面添加菜单项  
添加菜单项资源res\menu\rate.xml，并导入菜单项图片资源到res\mipmap\settings.png，修改rate.xml文件，添加菜单项配置如下

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/menu_set"
        android:icon="@mipmap/settings"
        android:title="@string/menu_set_label"
        app:showAsAction="ifRoom" />
</menu>
```

修改RateActivity启用菜单并响应菜单事件，添加代码如下

```
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.rate,menu);
    return true;
}

@Override
public boolean onOptionsItemSelected(MenuItem item) {
    if(item.getItemId()==R.id.menu_set){
        //点击后的事件处理，可填入打开配置汇率页面的代码
    }

    return super.onOptionsItemSelected(item);
}
```

当前程序每次使用时初始汇率都为0，想办法保存之前设置好的汇率值

### 参考材料

app/android:showAsAction的区别  

app:showAsAction使用时需要添加xmlns如下

> xmlns:app="[http://schemas.android.com/apk/res-auto](http://schemas.android.com/apk/res-auto)"

它有三个可选项

1. always：总是显示在界面上   
2. never：不显示在界面上，只让出现在右边的三个点中   
3. ifRoom：如果有位置才显示，不然就出现在右边的三个点中  

android:showAsAction  
这个属性可接受的值有：

1. alaways:这个值会使菜单项一直显示在ActionBar上  
2. ifRoom:如果有足够的空间,这个值会使菜单显示在ActionBar上  
3. never:这个值菜单永远不会出现在ActionBar是  
4. withText:这个值使菜单和它的图标，菜单文本一起显示 
 
//父类为AppCompatActivity 需要使用app:showAsAction  
> public class MainActivity extends AppCompatActivity {

//父类为Activity 需要使用android:showAsAction  
> public class MainActivity extends Activity{








# 完成汇率保存

> 实验目的
>
> 掌握SharedPreferences的使用理解数据保存方法

### 实验步骤

修改RateActivity，使用SharedPreferences保存数据，在onCreate方法里添加读取方法，从myrate文件中读取数据
```
SharedPreferences sharedPreferences = getSharedPreferences("myrate", Activity.MODE_PRIVATE);

dollarRate = sharedPreferences.getFloat("dollar_rate",0.0f);
euroRate = sharedPreferences.getFloat("euro_rate",0.0f);
wonRate = sharedPreferences.getFloat("won_rate",0.0f);

Log.i(TAG, "onCreate: sp dollarRate=" + dollarRate);
Log.i(TAG, "onCreate: sp euroRate=" + euroRate);
Log.i(TAG, "onCreate: sp wonRate=" + wonRate);
```
这是用于从SharedPreferences里读取数据，第一次读取时myrate不存在，则使用默认值赋值给变量
在完成汇率修改后，需要更新SharedPreferences里之前保存的汇率值，可以在ConfigActivity的保存按钮里处理，也可以放在RateActivity接收数据返回时处理，此处修改onActivityResult方法，添加保存数据的代码
```
//将新设置的汇率写到SP里
SharedPreferences sharedPreferences = getSharedPreferences("myrate", Activity.MODE_PRIVATE);
SharedPreferences.Editor editor = sharedPreferences.edit();
editor.putFloat("dollar_rate",dollarRate);
editor.putFloat("euro_rate",euroRate);
editor.putFloat("won_rate",wonRate);
editor.commit();
Log.i(TAG, "onActivityResult: 数据已保存到sharedPreferences");
```
汇率的保存工作已完成，需要注意的是，保存里写的文件要和读取的文件名一致，运行程序，验证功能


### 扩展练习

练习使用

> SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(this);

并总结出有何异同


### 参考材料
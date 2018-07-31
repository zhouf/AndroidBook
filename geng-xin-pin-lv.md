# 设置汇率更新频率

> 实验目的
>
> 设置应用程序更新汇率的频率为每天一次，能灵活运用各组件的基本用法以实现程序功能

### 实验步骤

基本思路：
为实现每天更新一次的频率，需要保存更新数据的日期，和当前日期进行比对，来确定当前日期是否已更新过数据，如果不需要更新，则不用启动子线程，日期数据同汇率数据一起保存在SharedPreferences里

在onCreate方法中，添加从SharedPreferences里获取保存的日期数据以及当时日期
```
String updateDate = sharedPreferences.getString("update_date","");

//获取当前系统时间
Date today = Calendar.getInstance().getTime();
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
final String todayStr = sdf.format(today);
```
然后进行日期判断
```
Log.i(TAG, "onCreate: sp updateDate=" + updateDate);
Log.i(TAG, "onCreate: todayStr=" + todayStr);

//判断时间
if(!todayStr.equals(updateDate)){
    Log.i(TAG, "onCreate: 需要更新");
    //开启子线程
    Thread t = new Thread(this);
    t.start();
}else{
    Log.i(TAG, "onCreate: 不需要更新");
}
```
同时需要修改汇率更新时代码，在保存新汇率同时保存更新日期，在handleMessage方法中添加如下代码，将当前日期保存在update_date中
```
//保存更新的日期
SharedPreferences sp = getSharedPreferences("myrate", Activity.MODE_PRIVATE);
SharedPreferences.Editor editor = sp.edit();
editor.putFloat("dollar_rate",dollarRate);
editor.putFloat("euro_rate",euroRate);
editor.putFloat("won_rate",wonRate);
editor.putString("update_date",todayStr);
editor.apply();
```
修改完毕，运行验证程序功能


### 扩展练习

实现这个功能不只一种方法，可考虑是否还有其它实现

### 参考材料

调试时可以在Android的程序管理中清除该程序的数据，此操作会清除SharedPreferences、数据库等用户数据，恢复到安装状态
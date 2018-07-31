# 解析汇率

> 实验目的
>
> 熟悉Jsoup解析html方法，完成汇率的提取

### 实验步骤

在工程中引入Jsoup包，修改build.gradle(Module: app)文件，在dependencies中加入引用
```
implementation 'org.jsoup:jsoup:1.11.3'
```
修改run方法，将之前获取的html字串交给Jsoup解析
```
Document doc = Jsoup.parse(html);
```
Jsoup除了接收html字串之外，也可以接收url地址，也可以直接把需要解析的url地址交给Jsoup，此处从http://www.usd-cny.com/bankofchina.htm获取数据，解析代码如下
```
Bundle bundle = new Bundle();
Document doc = null;
try {
    String url = "http://www.usd-cny.com/bankofchina.htm";
    doc = Jsoup.connect(url).get();
    Log.i(TAG, "run: " + doc.title());
    Elements tables = doc.getElementsByTag("table");

    Element table6 = tables.get(5);
    //Log.i(TAG, "run: table6=" + table6);
    //获取TD中的数据
    Elements tds = table6.getElementsByTag("td");
    for(int i=0;i<tds.size();i+=8){
        Element td1 = tds.get(i);
        Element td2 = tds.get(i+5);

        String str1 = td1.text();
        String val = td2.text();

        Log.i(TAG, "run: " + str1 + "==>" + val);

        float v = 100f / Float.parseFloat(val);
        if("美元".equals(str1)){
            bundle.putFloat("dollar-rate", v);
        }else if("欧元".equals(str1)){
            bundle.putFloat("euro-rate", v);
        }else if("韩国元".equals(str1)){
            bundle.putFloat("won-rate", v);
        }
    }

} catch (IOException e) {
    e.printStackTrace();
}
```
因为需要带回多个汇率数据，可以放入Bundle带回
```
Message msg = handler.obtainMessage(5);
msg.obj = bundle;
handler.sendMessage(msg);
```
主线程收到消息后，更新汇率
```
handler = new Handler(){
    @Override
    public void handleMessage(Message msg) {
        if(msg.what==5){
            Bundle bdl = (Bundle) msg.obj;
            dollarRate = bdl.getFloat("dollar-rate");
            euroRate = bdl.getFloat("euro-rate");
            wonRate = bdl.getFloat("won-rate");

            Log.i(TAG, "handleMessage: dollarRate:" + dollarRate);
            Log.i(TAG, "handleMessage: euroRate:" + euroRate);
            Log.i(TAG, "handleMessage: wonRate:" + wonRate);
            Toast.makeText(RateActivity.this, "汇率已更新", Toast.LENGTH_SHORT).show();
        }
        super.handleMessage(msg);
    }
};
```
如果考虑到汇率更新需要保存到SharedPreferences里，可以添加如下代码
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
程序修改结束，确保网络正常，调试运行


### 扩展练习

如果数据来源为其它页面，试着解析出需要的数据
http://www.usd-cny.com/icbc.htm
http://www.boc.cn/sourcedb/whpj/


### 参考材料
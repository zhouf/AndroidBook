# 列表的显示

> 实验目的
>
> 实现应用程序列表的显示

### 实验步骤

创建一个页面RateListActivity，继承于ListActivity，在类中定义三个基本数据
```
private String[] list_data = {"one","tow","three","four"};
int msgWhat = 3;
Handler handler;
```
第一个list_data是计划显示在列表中的数据项，该数据项可以是一个List<String>列表，也可以是一个String[]数组
第二个msgWhat是消息标识
第三个handler是用于接收子线程的消息处理
因为其父类ListActicity中已经有默认的布局，这里不需要再加载布局文件，去掉下面语句
```
//setContentView(R.layout.activity_rate_list);
```
构造一个ArrayAdapter用于处理数据和页面的关联
```
ListAdapter adapter = new ArrayAdapter<String>(RateListActivity.this,android.R.layout.simple_list_item_1,list_data);
setListAdapter(adapter);
```
第一个参数为当前Activity
第二个参数为列表项布局文件，此处调用Android系统内布局文件，所以是android.R里的内容
第三个参数为将要显示的数据项
准备好这几个参数创建好adapter，然后交由setListAdapter()方法去完成，此方法是已在父类ListActivity中已定义好，直接调用就好。
运行程序，显示页面，应该能看到列表显示效果。

接下来我们处理网络数据的获取
用之前的方法，创建线程，使用handler来处理子线程的数据。修改run方法，返回一个包含数据的List对象，修改后的run方法如下
```
@Override
public void run() {
    Log.i("thread","run.....");
    List<String> rateList = new ArrayList<String>();
    try {
        Document doc = Jsoup.connect("http://www.usd-cny.com/icbc.htm").get();

        Elements tbs = doc.getElementsByClass("tableDataTable");
        Element table = tbs.get(0);

        Elements tds = table.getElementsByTag("td");
        for (int i = 0; i < tds.size(); i+=5) {
            Element td = tds.get(i);
            Element td2 = tds.get(i+3);

            String tdStr = td.text();
            String pStr = td2.text();
            rateList.add(tdStr + "=>" + pStr);

            Log.i("td",tdStr + "=>" + pStr);
        }
    } catch (MalformedURLException e) {
        Log.e("www", e.toString());
        e.printStackTrace();
    } catch (IOException e) {
        Log.e("www", e.toString());
        e.printStackTrace();
    }

    Message msg = handler.obtainMessage(5);

    msg.obj = rateList;
    handler.sendMessage(msg);

    Log.i("thread","sendMessage.....");
}
```
修改主线程的handleMessage处理接收子线程返回的数据
```
handler = new Handler(){
    @Override
    public void handleMessage(Message msg) {
        if(msg.what == 5){
            List<String> retList = (List<String>) msg.obj;
            ListAdapter adapter = new ArrayAdapter<String>(RateListActivity.this,android.R.layout.simple_list_item_1,retList);
            setListAdapter(adapter);
            Log.i("handler","reset list...");
        }
        super.handleMessage(msg);
    }
};
```
别忘记添加Runnable接口，并开启线程，修改完成后，运行程序，查看从数据库中返回的数据项


### 扩展练习

通过在Activity中放入ListView控件实现列表的显示

### 参考材料
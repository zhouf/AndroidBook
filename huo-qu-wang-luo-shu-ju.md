# 使用多线程获取网络数据

> 实验目的
>
> 了解多线程的使用，掌握Handler消息传递机制，学会使用Handler进行线程间消息同步

### 实验步骤
在RateActivity中添加Runnable接口，并实现run方法
```
public class RateActivity extends AppCompatActivity implements Runnable{

public void run() {
    Log.i(TAG, "run: run()......");
}
```
添加一类变量Handler handler
```
Handler handler;
```
在onCreate方法中开启子线程，并重写handleMessage方法
```
//开启子线程
Thread t = new Thread(this);
t.start();

handler = new Handler(){
    @Override
    public void handleMessage(Message msg) {
        
        }
        super.handleMessage(msg);
    }
};
```
修改run方法，在子线程里向主线程返回消息
```
Log.i(TAG, "run: run()......");
for(int i=1;i<3;i++){
    Log.i(TAG, "run: i=" + i);
    try {
        Thread.sleep(2000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}

//获取Msg对象，用于返回主线程
Message msg = handler.obtainMessage(5);
//msg.what = 5;
msg.obj = "Hello from run()";
handler.sendMessage(msg);
```
通过循环模拟延时，返回一个字串，修改handleMessage方法，处理返回的消息
```
public void handleMessage(Message msg) {
    if(msg.what==5){
        String str = (String) msg.obj;
        Log.i(TAG, "handleMessage: getMessage msg = " + str);
        show.setText(str);
    }
    super.handleMessage(msg);
}
```
至此，子线程已可成功带回消息，接下来获取网络数据，修改run方法，添加如下代码
```
//获取网络数据
URL url = null;
try {
    url = new URL("http://www.usd-cny.com/icbc.htm");
    HttpURLConnection http = (HttpURLConnection) url.openConnection();
    InputStream in = http.getInputStream();

    String html = inputStream2String(in);
    Log.i(TAG, "run: html=" + html);

} catch (MalformedURLException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
}
```
将输入流InputStream转换为String的方法有很多，可参考下面的代码实现
```
private String inputStream2String(InputStream inputStream) throws IOException {
    final int bufferSize = 1024;
    final char[] buffer = new char[bufferSize];
    final StringBuilder out = new StringBuilder();
    Reader in = new InputStreamReader(inputStream, "gb2312");
    while (true) {
        int rsz = in.read(buffer, 0, buffer.length);
        if (rsz < 0)
            break;
        out.append(buffer, 0, rsz);
    }
    return out.toString();
}
```
此时程序需要访问网络，修改AndroiManifest.xml文件，添加网络访问权限，在application标签之前加入以下代码
```
<uses-permission android:name="android.permission.INTERNET"/>
```
测试运行程序 ，查看在Logcat中是否已有输入获取的文本内容


### 使用Handler实现欢迎页

Handler可以用于处理消息，其中postDelayed可以用于延时消息传送，可以利用此功能实现欢迎页面，达到延时跳转到首页的效果，此处假定从WelcomeActivity跳转到MainActivity，在onCreate中可加入如下代码
```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_welcome);

    new Handler().postDelayed(new Runnable() {
        @Override
        public void run() {
            Intent mainIntent = new Intent(WelcomeActivity.this, MainActivity.class);
            startActivity(mainIntent);
            finish();
        }
    },3000);//3000毫秒后执行，即3秒跳转
}
```
如果欢迎面需要实现全屏，不需要TitleBar的话，可以配置在activity配置中加入theme实现
```
<style name="AppTheme.NoActionBar">
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
</style>
```
在AndroidManifest.xml中使用该配置
```
<activity android:name=".WelcomeActivity"
    android:theme="@style/AppTheme.NoActionBar">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```
也可直接使用Theme.AppCompat.NoActionBar



### 扩展练习

如何从已获得的数据中提取出需要的汇率数据


### 参考材料
https://jsoup.org/

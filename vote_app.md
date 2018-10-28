# 完成投票应用程序

> 目标：
>
> 掌握App客户端与服务器端的数据传递，了解应用程序与服务器通讯的基本原理

### 第一步：准备好服务器端

完成一个完整的服务器端应用，可以通过Java,Go,Python,Nodejs等实现，并测试可以正常运行，自行约定与服务器的数据交换方式，可以是json,text等
> 可下载并解压示例程序[vote.war](https://github.com/zhouf/vote/releases/download/v1.01/vote_v101.zip)并部署运行
> 此处假设已准备好服务器，且服务器地址为：http://192.168.1.102:8080/
> 接收数据输入接口为：http://192.168.1.102:8080/vote/GetVote
可以在其它电脑上选用接口测试工具（如PostMan）测试接口是否正常工作，接口正常工作后可开始手机端的程序


### 第二步：完成手机端页面

创建一个新的工程，完成一个投票页面MainActivity，在页面上放三个按钮，分别是投票，弃权，反对，布局文件activity_main.xml如下

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/LinearLayout1"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:paddingBottom="16dp"
    android:paddingLeft="16dp"
    android:paddingRight="16dp"
    android:paddingTop="16dp"
    tools:context=".MainActivity" >

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello_world"
        android:gravity="center"
        android:textSize="30sp" />

    <Button
        android:id="@+id/btn_approve"
        android:layout_width="160dp"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="@string/lab_approve" />

    <Button
        android:id="@+id/btn_object"
        android:layout_width="160dp"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="@string/lab_object" />

    <Button
        android:id="@+id/btn_abstain"
        android:layout_width="160dp"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="@string/lab_abstain" />

</LinearLayout>
```
需要添加如下字符串资源
```
<string name="hello_world">欢迎使用！</string>
<string name="lab_approve">赞成</string>
<string name="lab_object">反对</string>
<string name="lab_abstain">弃权</string>
```

### 第三步：实现网络请求的异步任务及封装方法

在MainActivity.java中实现一个子类VoteTask,继承于AsyncTask,并实现其方法

```
private class VoteTask extends AsyncTask<String, Void, String> {

    @Override
    protected String doInBackground(String... params) {
        for (String p: params ) {
            Log.i(TAG, "doInBackground: " + p);
        }
        String ret = doVote(params[0]);
        return ret;
    }

    @Override
    protected void onPostExecute(String s) {
        Toast.makeText(MainActivity.this, s, Toast.LENGTH_SHORT).show();
    }
}
```
在doInBackground中调用了doVote方法，实现将数据提交到服务器上，具体实现代码如下

```
private String doVote(String voteStr){
    String retStr = "";
    Log.i("vote", "doVote() voteStr:" + voteStr);

    try {

        StringBuffer stringBuffer = new StringBuffer();        //存储封装好的请求体信息
        stringBuffer.append("r=").append(URLEncoder.encode(voteStr, "utf-8"));

        byte[] data = stringBuffer.toString().getBytes();
        String urlPath = "http://192.168.1.102:8080/vote/GetVote";
        URL url = new URL(urlPath);

        HttpURLConnection httpURLConnection = (HttpURLConnection)url.openConnection();
        httpURLConnection.setConnectTimeout(3000);     //设置连接超时时间
        httpURLConnection.setDoInput(true);                  //打开输入流，以便从服务器获取数据
        httpURLConnection.setDoOutput(true);                 //打开输出流，以便向服务器提交数据
        httpURLConnection.setRequestMethod("POST");     //设置以Post方式提交数据
        httpURLConnection.setUseCaches(false);               //使用Post方式不能使用缓存
        //设置请求体的类型是文本类型
        httpURLConnection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        //设置请求体的长度
        httpURLConnection.setRequestProperty("Content-Length", String.valueOf(data.length));
        //获得输出流，向服务器写入数据
        OutputStream outputStream = httpURLConnection.getOutputStream();
        outputStream.write(data);

        int response = httpURLConnection.getResponseCode();            //获得服务器的响应码
        if(response == HttpURLConnection.HTTP_OK) {
            InputStream inputStream = httpURLConnection.getInputStream();
            retStr = inputStreamToString(inputStream);                     //处理服务器的响应结果
            Log.i("vote", "retStr:" + retStr);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    return retStr;
}
```
> 注意修改服务器地址为第一步配置的地址

输入流转字符串的方法如下所示

```
public static String inputStreamToString(InputStream inputStream) {
    String resultData = null;      //存储处理结果
    ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
    byte[] data = new byte[1024];
    int len = 0;
    try {
        while((len = inputStream.read(data)) != -1) {
            byteArrayOutputStream.write(data, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    resultData = new String(byteArrayOutputStream.toByteArray());
    return resultData;
}
```

### 第四步：开启网络访问权限

此程序需要访问网络，需要开启网络访问权限，修改AndroidManifest.xml文件，添加如下配置
```
<uses-permission android:name="android.permission.INTERNET"/>
```

测试运行程序
> 注意不要使用localhost，即使在同一电脑上运行模拟器和AndroidStudio，注意电脑端防火墙设置
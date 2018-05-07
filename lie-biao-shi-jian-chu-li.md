# 列表事件处理

> 掌握列表事件的处理

### 列表单击事件

如果让列表响应单击事件，需要为列表添加事件监听`setOnItemClickListener()`，如果当前Activity的父类是ListActivity，那么需要通过getListView\(\)方法获取listView对象。如添加当前Activity对象为监听器，那么当前Activity需要实现`AdapterView.OnItemClickListener`接口，代码如下

```
public class MyList2Activity extends ListActivity implements AdapterView.OnItemClickListener {

    ...

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        getListView().setOnItemClickListener(this);
    }
```

在实现的接口方法里写入执行代码，获取当前的币种和汇率，打开新的计算页面

```
public void onItemClick(AdapterView<?> parent, View view, int position, long id) {

    Log.i(TAG, "onItemClick: parent=" + parent);
    Log.i(TAG, "onItemClick: view=" + view);
    Log.i(TAG, "onItemClick: position=" + position);
    Log.i(TAG, "onItemClick: id=" + id);

    //从ListView中获取选中数据
    HashMap<String,String> map = (HashMap<String, String>) getListView().getItemAtPosition(position);
    String titleStr = map.get("ItemTitle");
    String detailStr = map.get("ItemDetail");
    Log.i(TAG, "onItemClick: titleStr=" + titleStr);
    Log.i(TAG, "onItemClick: detailStr=" + detailStr);

    //从View中获取选中数据
    TextView title = (TextView) view.findViewById(R.id.itemTitle);
    TextView detail = (TextView) view.findViewById(R.id.itemDetail);
    String title2 = String.valueOf(title.getText());
    String detail2 = String.valueOf(detail.getText());
    Log.i(TAG, "onItemClick: title2=" + title2);
    Log.i(TAG, "onItemClick: detail2=" + detail2);

    //打开新的页面，传入参数
    Intent rateCalc = new Intent(this,RateCalcActivity.class);
    rateCalc.putExtra("title",titleStr);
    rateCalc.putExtra("rate",Float.parseFloat(detailStr));
    startActivity(rateCalc);

}
```

将币种文本和汇率数据传入到RateCalcActivity页面，附RateCalcActivity布局和代码

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_rate_calc"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.swufe.hello.RateCalcActivity">

    <TextView
        android:id="@+id/title2"
        android:textSize="35sp"
        android:gravity="center"
        android:text="@string/app_name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <EditText
        android:id="@+id/inp2"
        android:hint="@string/rmb_hint_label"
        android:inputType="numberDecimal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <TextView
        android:id="@+id/show2"
        android:gravity="center"
        android:textSize="25sp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

JAVA源码如下

```
package com.swufe.hello;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.text.Editable;
import android.text.TextWatcher;
import android.util.Log;
import android.widget.EditText;
import android.widget.TextView;

public class RateCalcActivity extends AppCompatActivity {

    String TAG = "rateCalc";
    float rate = 0f;
    EditText inp2;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_rate_calc);
        String title = getIntent().getStringExtra("title");
        rate = getIntent().getFloatExtra("rate",0f);

        Log.i(TAG, "onCreate: title = " + title);
        Log.i(TAG, "onCreate: rate=" + rate);
        ((TextView)findViewById(R.id.title2)).setText(title);
        inp2 = (EditText)findViewById(R.id.inp2);
        inp2.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {

            }

            @Override
            public void afterTextChanged(Editable s) {
                TextView show = (TextView) RateCalcActivity.this.findViewById(R.id.show2);
                if(s.length()>0){
                    float val = Float.parseFloat(s.toString());
                    show.setText(val + "RMB==>" + (100/rate*val));
                }else{
                    show.setText("");
                }

            }
        });
    }
}
```

为了在输入时就可以响应，使用了TextWatcher监听数据输入

### 长按处理

处理列表项长按和单击类似，只是实现的方法不同，长按需要做如下处理

* 为ListView控件添加事件监听.setOnItemLongClickListener\(this\);
* 实现接口AdapterView.OnItemLongClickListener
* 实现接口方法onItemLongClick




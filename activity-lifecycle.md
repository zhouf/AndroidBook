# Activity生命周期

> 实验目的
>
> 了解Activity生命周期及旋转时的数据保存

### 实验步骤

修改SecondActivity，修改onCreate方法并重写如下方法
```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_second);
    Log.i(TAG, "onCreate: ");
}

@Override
protected void onStart() {
    super.onStart();
    Log.i(TAG, "onStart: ");
}

@Override
protected void onResume() {
    super.onResume();
    Log.i(TAG, "onResume: ");
}

@Override
protected void onRestart() {
    super.onRestart();
    Log.i(TAG, "onRestart: ");
}

@Override
protected void onPause() {
    super.onPause();
    Log.i(TAG, "onPause: ");
}

@Override
protected void onStop() {
    super.onStop();
    Log.i(TAG, "onStop: ");
}

@Override
protected void onDestroy() {
    super.onDestroy();
    Log.i(TAG, "onDestroy: ");
}
```
在每个方法中添加输出，为了观察程序运行的状态，运行程序，旋转屏幕，并观察日志输出结果。
在屏幕旋转时为保存分数状态，重写如下方法，在屏幕方向改变时保存数据到Bundle
```
@Override
protected void onSaveInstanceState(Bundle outState) {
    super.onSaveInstanceState(outState);
    String scorea = ((TextView)findViewById(R.id.score)).getText().toString();
    String scoreb = ((TextView)findViewById(R.id.score2)).getText().toString();

    Log.i(TAG, "onSaveInstanceState: ");
    outState.putString("teama_score",scorea);
    outState.putString("teamb_score",scoreb);
}
```
在屏幕完成旋转时恢复数据，重写如下方法实现
```
@Override
protected void onRestoreInstanceState(Bundle savedInstanceState) {
    super.onRestoreInstanceState(savedInstanceState);
    String scorea = savedInstanceState.getString("teama_score");
    String scoreb = savedInstanceState.getString("teamb_score");

    Log.i(TAG, "onRestoreInstanceState: ");
    ((TextView)findViewById(R.id.score)).setText(scorea);
    ((TextView)findViewById(R.id.score2)).setText(scoreb);
}
```
修改完成，运行程序验证


### 扩展练习

修改页面布局，添加一水平方向布局，再验证旋转数据是否保留

### 参考材料
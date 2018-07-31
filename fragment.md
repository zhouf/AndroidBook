# Fragment界面处理

> 目标：
>
> 学习掌握Fragment组件的基本用法，掌握Fragment切换的方法

### 第一步：创建各Fragment页面布局

创建三个布局文件frame\_home.xml,frame\_func.xml,frame\_setting.xml

在布局文件中添加组件，此处为演示，仅添加一TextView，并设置其ID，frame\_home.xml代码如下

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_margin="@dimen/activity_vertical_margin"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/homeTextView1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello_world"
        android:textSize="30sp" />

</LinearLayout>
```

frame\_func.xml和frame\_setting.xml内容与此相同，仅TextView的id值不同，分别为

```
android:id="@+id/funcTextView1"
```

和

```
android:id="@+id/settingTextView1"
```

### 第二步：创建Fragment页面代码

在工程包中创建分别创建三个类HomeFragment,FuncFragment,SettingFragment，继承于android.support.v4.app.Fragment类，并重载onCreateView方法，使当前Fragment加载相应的页面布局文件，此处以HomeFragment.java为例，重载后的方法如下

```
@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container,
		Bundle savedInstanceState) {
	return inflater.inflate(R.layout.frame_home, container);
}
```

然后重载onActivityCreated方法，当Activity创建时会调用，完成对控件的初始化，添加事件监听等，重载后的方法如下

```
@Override
public void onActivityCreated(Bundle savedInstanceState) {
	super.onActivityCreated(savedInstanceState);
	TextView tv = (TextView)getView().findViewById(R.id.homeTextView1);
	tv.setText("这是主页面");
}
```

FuncFragment.java和SettingFragment.java以此类推，注意引用不同的layout文件和不同的TextView控件id

至此，三个需要显示的Fragment页面已准备好了

### 第三步：创建容器页面

使用向导创建一Activity页面FrameActivity，用于包含三个Fragment页面及切换的按钮，向导会生成相应的页面布局文件activity\_frame.xml

在activity\_frame.xml中添加三个Fragment组件，并分别设置其id和name属性，之后再添加一组Radio，用于对Fragment切换的控制

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/LinearLayout1"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".FrameActivity" >

    <fragment
        android:id="@+id/fragment_main"
        android:name="com.zhouf.hello.HomeFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent" 
        android:layout_weight="10" />

    <fragment
        android:id="@+id/fragment_func"
        android:name="com.zhouf.hello.FuncFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="10" />

    <fragment
        android:id="@+id/fragment_setting"
        android:name="com.zhouf.hello.SettingFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="10" />

    <RadioGroup
        android:id="@+id/bottomGroup"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" 
        android:layout_weight="0.5"
        android:orientation="horizontal">

        <RadioButton
            android:id="@+id/radioHome"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/rdo_home_label" />

        <RadioButton
            android:id="@+id/radioFunc "
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/rdo_func_label" />

        <RadioButton
            android:id="@+id/radioSetting "
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/rdo_setting_label" />
        
    </RadioGroup>
</LinearLayout>
```

设置三个RadioButton按钮文本分别为

```
<string name="rdo_home_label">首页</string>
<string name="rdo_func_label">功能</string>
<string name="rdo_setting_label">设置</string>
```

### 第四步：实现页面编码

需要修改Activity的父类为android.support.v4.app.FragmentActivity，并在onCreate方法里获取页面中的所有控件，三个Fragment以及Radio组件，其实现代码如下

```
public class FrameActivity extends FragmentActivity {
	
	private Fragment mFragments[];
	private RadioGroup radioGroup;
	private FragmentManager fragmentManager;
	private FragmentTransaction fragmentTransaction;
	private RadioButton rbtHome,rbtFunc,rbtSetting;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.activity_frame);
		mFragments = new Fragment[3];
		fragmentManager = getSupportFragmentManager();
		mFragments[0] = fragmentManager.findFragmentById(R.id.fragment_main);
		mFragments[1] = fragmentManager.findFragmentById(R.id.fragment_func);
		mFragments[2] = fragmentManager.findFragmentById(R.id.fragment_setting);
		
		fragmentTransaction = fragmentManager.beginTransaction().hide(mFragments[0]).hide(mFragments[1]).hide(mFragments[2]);
		fragmentTransaction.show(mFragments[0]).commit();
		
		radioGroup = (RadioGroup)findViewById(R.id.bottomGroup);
		radioGroup.setOnCheckedChangeListener(new OnCheckedChangeListener() {
			
			@Override
			public void onCheckedChanged(RadioGroup group, int checkedId) {
				Log.i("radioGroup", "checkId=" + checkedId);
				fragmentTransaction = fragmentManager.beginTransaction().hide(mFragments[0]).hide(mFragments[1]).hide(mFragments[2]);
				
				switch(checkedId){
				case R.id.radioHome:
					fragmentTransaction.show(mFragments[0]).commit();
					break;
				case R.id.radioFunc:
					fragmentTransaction.show(mFragments[1]).commit();
					break;
				case R.id.radioSetting:
					fragmentTransaction.show(mFragments[2]).commit();
					break;
				default:
					break;	
				}
			}
		});
	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.frame, menu);
		return true;
	}

}
```

此时可测试页面基本功能，可把当前Activity设置为应用程序的起始页面，或是在起始页面中加入按钮功菜单跳转到当前页面，如果基本功能没有问题，可调整美化页面结构

### 第五步：修改按钮风格

添加三个图片到res\drawable-mdpi中，分别是home.png，panda.png，setting.png，另外在此目录中创建两个shape文件，用于表示按钮组背景颜色和选中按钮背景色，分别是shape2.xml

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >

    <gradient
        android:angle="90"
        android:endColor="#11777777"
        android:startColor="#FF000000" />

</shape>
```

和shape3.xml

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >

    <gradient
        android:angle="270"
        android:endColor="#11777777"
        android:startColor="#FF000000" />

</shape>
```

此处只是用渐变的方式做了处理，shape2和shape3的颜色方向刚好相反，也可以通过调换startColor和endColor来实现

如果使用纯色填充，shape2和shape3分别如下
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <solid android:color="#CCCCFF" />
</shape>
```
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <solid android:color="#99CCCC" />
</shape>
```



因三个Radio按钮风格都要修改，可在res\values\style.xml中添加一个按钮样式rg\_btn\_style，定义如下

```
<style name="rg_btn_style">
    <item name="android:button">@null</item>
    <item name="android:gravity">center</item>
    <item name="android:layout_gravity">center</item>
    <item name="android:layout_weight">2</item>
    <item name="android:textSize">10sp</item>
    <item name="android:drawablePadding">1dp</item>
    <item name="android:layout_width">match_parent</item>
    <item name="android:padding">7dp</item>
    <!--
    <item name="android:layout_marginBottom">4dp</item>
    -->
    <item name="android:background">@drawable/shape2</item>
</style>
```

此样式设置为去掉Radio的圆形按钮，及对文本和图片的设置，添加背景等处理

修改页面布局文件，应用Radio样式，将activity\_frame.xml中的Radio部分做如下调整

```
<RadioGroup
    android:id="@+id/bottomGroup"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" 
    android:layout_weight="0.5"
    android:orientation="horizontal">

    <RadioButton
        android:id="@+id/radioHome"
        style="@style/rg_btn_style"
        android:drawableTop="@drawable/home"
        android:text="@string/rdo_home_label" />

    <RadioButton
        android:id="@+id/radioFunc"
        style="@style/rg_btn_style"
        android:drawableTop="@drawable/panda"
        android:text="@string/rdo_func_label" />

    <RadioButton
        android:id="@+id/radioSetting"
        style="@style/rg_btn_style"
        android:drawableTop="@drawable/setting"
        android:text="@string/rdo_setting_label" />
    
</RadioGroup>
```

添加代码切换的响应，点击不同的切换按钮时，通过设置不同的背景加以区分

```
rbtHome = (RadioButton)findViewById(R.id.radioHome);
rbtFunc = (RadioButton)findViewById(R.id.radioFunc);
rbtSetting = (RadioButton)findViewById(R.id.radioSetting);
rbtHome.setBackgroundResource(R.drawable.shape3);

radioGroup = (RadioGroup)findViewById(R.id.bottomGroup);
radioGroup.setOnCheckedChangeListener(new OnCheckedChangeListener() {
	
	@Override
	public void onCheckedChanged(RadioGroup group, int checkedId) {
		Log.i("radioGroup", "checkId=" + checkedId);
		fragmentTransaction = fragmentManager.beginTransaction().hide(mFragments[0]).hide(mFragments[1]).hide(mFragments[2]);
		rbtHome.setBackgroundResource(R.drawable.shape2);
		rbtFunc.setBackgroundResource(R.drawable.shape2);
		rbtSetting.setBackgroundResource(R.drawable.shape2);
		
		switch(checkedId){
		case R.id.radioHome:
			fragmentTransaction.show(mFragments[0]).commit();
			rbtHome.setBackgroundResource(R.drawable.shape3);
			break;
		case R.id.radioFunc:
			fragmentTransaction.show(mFragments[1]).commit();
			rbtFunc.setBackgroundResource(R.drawable.shape3);
			break;
		case R.id.radioSetting:
			fragmentTransaction.show(mFragments[2]).commit();
			rbtSetting.setBackgroundResource(R.drawable.shape3);
			break;
		default:
			break;	
		}
	}
});
```

如果要去掉Title部分，需要在setContentView\(\)语句前添加

```
requestWindowFeature(Window.FEATURE_NO_TITLE);
```

测试页面风格，并对需要修改的地方进行调整

### 补充：android.support.v4.app.Fragment和android.app.Fragment区别

1.最低支持版本不同

Android.app.Fragment 兼容的最低版本是android:minSdkVersion="11" 即3.0版

android.support.v4.app.Fragment 兼容的最低版本是android:minSdkVersion="4" 即1.6版

2.需要导jar包

fragment android.support.v4.app.Fragment 需要引入包android-support-v4.jar

3.在Activity中取的方法不同

android.app.Fragment使用\(ListFragment\)getFragmentManager\(\).findFragmentById\(R.id.userList\)  获得，继承Activity

android.support.v4.app.Fragment使用\(ListFragment\)getSupportFragmentManager\(\).findFragmentById\(R.id.userList\)获得，需要继承android.support.v4.app.FragmentActivity

### 扩展练习：

完善界面，添加各Fragment内容及功能




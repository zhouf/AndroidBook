# 完成偏好设置窗口

> 目标：
>
> 学习掌握设置页面的使用，掌握页面布局的基本方法

### 第一步：创建设置页面

在工程中新增一个Activity,SettingsActivity,并在SettingsActivity中添加PreferenceFragment的实现类

```
package com.swufe.hello;

import android.os.Bundle;
import android.preference.PreferenceFragment;
import android.support.v7.app.AppCompatActivity;

public class SettingsActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.settings_activity);
    }

    public static class MyPreferenceFragment extends PreferenceFragment {

    }
}
```
在 settings_activity.xml 中定义设置活动的布局：

```
<?xml version="1.0" encoding="utf-8"?>

<fragment
    android:name="com.swufe.hello.SettingsActivity$MyPreferenceFragment"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.swufe.hello.SettingsActivity">
</fragment>
```
将类中的fragment写在android:name中，此处没有外层布局项

在AndroidManifest.xml中声明新的Activity，并将父窗口指向之前的MainActivity

```
<activity
    android:name=".SettingsActivity"
    android:label="@string/settings_title">
    <meta-data
        android:name="android.support.PARENT_ACTIVITY"
        android:value="com.swufe.hello.MainActivity"/>
</activity>
```
在当前Activity配置中添加了android:name="android.support.PARENT_ACTIVITY"的meta-data后，会在标题行出现返回父窗口的导航，再将打开此SettingsActivity页面的操作绑定到MainActivity的菜单项中

### 第二步：配置偏好项

在strings.xml中添加如下字符串资源
```
<string name="settings_title">配置页面</string>

<!-- 控件标签 -->
<string name="set_switch1_label">开关项</string>
<string name="set_checkbox1_label">复选项</string>
<string name="set_list_label">列表项</string>
<string name="set_text1_label">选项1</string>
<string name="set_text2_label">选项2</string>
<string name="set_group1_label">分组1</string>
<string name="set_group2_label">分组2</string>

<!-- 控件ID -->
<string name="set_switch1_key">set_switch1</string>
<string name="set_checkbox1_key">set_checkbox1</string>
<string name="set_list_key">set_list</string>
<string name="set_text1_key">set_text1</string>
<string name="set_text2_key">set_text2</string>
```
在value目录下创建arrays.xml资源文件，如果已存在则直接添加内容，内容为下拉列表的选项，分别为标签和值

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="setting_list_label">
        <item>每月</item>
        <item>每周</item>
        <item>每天</item>
        <item>每小时</item>
    </string-array>

    <string-array name="setting_list_value">
        <item>monthly</item>
        <item>weekly</item>
        <item>daily</item>
        <item>hourly</item>
    </string-array>
</resources>
```

偏好设置页面的内容都是通过xml文件配置的，在res目录下创建xml资源目录，创建时选择Resource type为xml，并在 res/xml 目录中创建XML resource file，命名为settings_main.xml 文件，该文件内容如下：


```
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    
    <PreferenceCategory android:title="@string/set_group1_label">
        <SwitchPreference
            android:key="@string/set_switch1_key"
            android:title="@string/set_switch1_label"
            android:defaultValue="true"/>

        <ListPreference
            android:key="@string/set_list_key"
            android:title="@string/set_list_label"
            android:entries="@array/setting_list_label"
            android:entryValues="@array/setting_list_value"
            android:summary="This is summary"
            android:defaultValue="daily"
            android:dependency="@string/set_switch1_key"/>

        <EditTextPreference
            android:key="@string/set_text1_key"
            android:title="@string/set_text1_label"
            android:inputType="number"
            android:defaultValue="1"
            android:selectAllOnFocus="true"
            android:dependency="@string/set_switch1_key"/>

    </PreferenceCategory>

    <PreferenceCategory android:title="@string/set_group2_label">

        <CheckBoxPreference
            android:key="@string/set_checkbox1_key"
            android:title="@string/set_checkbox1_label"
            android:defaultValue="false"/>

        <EditTextPreference
            android:key="@string/set_text2_key"
            android:title="@string/set_text2_label"
            android:inputType="text"
            android:defaultValue="Hello"
            android:selectAllOnFocus="false"
            android:dependency="@string/set_checkbox1_key"/>

    </PreferenceCategory>
</PreferenceScreen>
```
* android:summary可以显示摘要信息，及对偏好项的描述
* android:selectAllOnFocus为是否选中文本，运行时注意选项1和选项2的表现区别
* android:dependency="use_setting"表示当前项依赖于use_setting控件，通常是CheckBox或Switch项

preference下的View是有限的，只有下面几个：
* CheckBoxPreference:CheckBox选择项，对应的值的ture或flase
* SwitchPreference:开关选择项，与checkbox类似，对应值为boolean型
* EditTextPreference:输入编辑框，值为String类型，会弹出对话框供输入。
* ListPreference: 列表选择，弹出对话框供选择。
* Preference：只进行文本显示，需要与其他进行组合使用。
* PreferenceCategory：用于分组。
* RingtonePreference：系统玲声选择


覆盖 MyPreferenceFragment 内部类中的 onCreate() 方法，以加载定义的 settings_mai.xml 资源


```
public class SettingsActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.settings_activity);
    }
    
    public static class EarthquakePreferenceFragment extends PreferenceFragment {
    
        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            addPreferencesFromResource(R.xml.settings_main);
        }
    }
}
```

### 第三步：使用偏好值

偏好的值会自动保存在默认的SharedPreference里，可以通过访问普通SharedPreference的方式获取值


```
SharedPreferences sharedPrefs = PreferenceManager.getDefaultSharedPreferences(this);
boolean setSwitch = sharedPrefs.getBoolean(getString(R.string.set_switch1_key),false);
String setVal2 = sharedPrefs.getString(getString(R.string.set_text2_key),"");
int setVal1 = Integer.parseInt(sharedPrefs.getString(getString(R.string.set_text1_key),"0"));
```
注意：自定义的SharedPreferences对象是可以放入数值类型数据，偏好设置里EditTextPreference保存的是String类型，如果需要获取数值类型，需要通过String进行类型转换

### 改进

对于我们的用户而言，通过单击设置项来查看其当前设置（比如选项1，选项2），体验并不太好。更好的方法是打开 Setting Activity 即可查看偏好名称下方的偏好值，如果对其进行更改，可以看到摘要将立即更新。
要实现该功能，当偏好发生更改时，PreferenceFragment 可以实现 OnPreferenceChangeListener 接口以获取通知。然后，当用户更改单个偏好并进行保存时，将使用已更改偏好的关键字来调用 onPreferenceChange() 方法。

请注意，此方法将返回布尔值，可防止通过返回 false 来更改建议的偏好设置，即返回false则不保存用户修改。

首先声明 MyPreferenceFragment 类应 实现 OnPreferenceChangeListener 接口，然后覆盖 onPreferenceChange() 方法。此方法中的代码 会在偏好更改后更新已显示的偏好摘要。添加一个 bindPreferenceSummaryToValue() 方法，用于指定绑定编好项

修改后的 SettingsActivity.java 代码如下：


```
package com.swufe.hello;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.preference.Preference;
import android.preference.PreferenceFragment;
import android.preference.PreferenceManager;
import android.support.v7.app.AppCompatActivity;

public class SettingsActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.settings_activity);
    }

    public static class MyPreferenceFragment extends PreferenceFragment implements Preference.OnPreferenceChangeListener {

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            addPreferencesFromResource(R.xml.settings_main);

            Preference setText1 = findPreference(getString(R.string.set_text1_key));
            bindPreferenceSummaryToValue(setText1);

            //设置选项2可显示
            //bindPreferenceSummaryToValue(findPreference(getString(R.string.set_text2_key)));

            //设置显示列表值，会替换掉原来的summary显示
            //bindPreferenceSummaryToValue(findPreference(getString(R.string.set_list_key)));
        }
        @Override
        public boolean onPreferenceChange(Preference preference, Object value) {
            String stringValue = value.toString();
            preference.setSummary(stringValue);
            return true;
        }

        private void bindPreferenceSummaryToValue(Preference preference) {
            preference.setOnPreferenceChangeListener(this);
            SharedPreferences preferences = PreferenceManager.getDefaultSharedPreferences(preference.getContext());
            String preferenceString = preferences.getString(preference.getKey(), "");
            onPreferenceChange(preference, preferenceString);
        }
    }
}
```

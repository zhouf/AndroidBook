# 应用样式修改

> 介绍样式修改的基本概念及简单操作

APP样式文件位于`res\values\themes`目录下，分涉及到两种模式Day-Night，所以有两个文件

## 一、样式文件

通常使用样式文件`themes.xml`来管理应用中界面风格，在`AndroidManifest.xml`文件中有对样式的引用
```
<application
    ....
    android:theme="@style/Theme.PrjName">
```
目前新的项目样式通常继承于`Theme.MaterialComponents`
```
<style name="Theme.PrjName" parent="Theme.MaterialComponents.DayNight.DarkActionBar">
```
如果项目样式还是`Theme.AppCompat`建议修改为`Theme.MaterialComponents`

## 二、修改项目默认样式

项目样式文件创建时为如下内容
```
<resources xmlns:tools="http://schemas.android.com/tools">
    <!-- Base application theme. -->
    <style name="Theme.PrjName" parent="Theme.MaterialComponents.DayNight.DarkActionBar">
        <!-- Primary brand color. -->
        <item name="colorPrimary">@color/purple_500</item>
        <item name="colorPrimaryVariant">@color/purple_700</item>
        <item name="colorOnPrimary">@color/white</item>
        <!-- Secondary brand color. -->
        <item name="colorSecondary">@color/teal_200</item>
        <item name="colorSecondaryVariant">@color/teal_700</item>
        <item name="colorOnSecondary">@color/black</item>
        <!-- Status bar color. -->
        <item name="android:statusBarColor" tools:targetApi="l">?attr/colorPrimaryVariant</item>
        <!-- Customize your theme here. -->
    </style>
</resources>
```
里面有颜色荐配置及颜色取值，颜色具体值放在`colors.xml`文件中，如果需要自己定义，可以在后面加入

例如：修改应用中按钮颜色，在上述文件中加入按钮配置`android:colorButtonNormal`，默认状态下，在`Theme.MaterialComponents.DayNight.DarkActionBar`中修改不生效，需要修改样式继承于`Theme.MaterialComponents.DayNight.DarkActionBar.Bridge`，方可在`Theme.PrjName`中使用自定义样式
首先在`colors.xml`文件中添加颜色定义
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    ....
    <color name="deep_orange">#F44336</color>
</resources>
```
在`themes.xml`中引用，建议采用引用方式，以方便颜色统一管理，不建议直接在样式文件中写入颜色代码
```
<resources xmlns:tools="http://schemas.android.com/tools">
    
    <style name="Theme.PrjName" parent="Theme.MaterialComponents.DayNight.DarkActionBar.Bridge">
        ....
        <!-- Customize your theme here. -->
        <item name="android:colorButtonNormal">@color/deep_orange</item>
    </style>
```
## 三、添加其它自定义样式

如果需要定义其它控件样式，可在样式文件中写入多个`<style>`，如加入文本样式，可继承一个父样式再修改
```
<resources xmlns:tools="http://schemas.android.com/tools">
    <!-- Base application theme. -->
    <style name="Theme.PrjName" parent="Theme.MaterialComponents.DayNight.DarkActionBar.Bridge">
        ...
    </style>

    <style name="MyLabel" parent="@android:style/TextAppearance">
        <item name="android:textColor">#795548</item>
        <item name="android:textSize">35sp</item>
    </style>
</resources>
```
然后在页面布局文件中调用，如需要修改文本样式为`MyLabel`样式风格，可直接设定其`style`属性
```

<TextView
    android:id="@+id/textView"
    style="@style/MyLabel"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="TextView" />
```
其它样式设定可参考如下内容



## 参考材料

[样式和主题背景（官网）](https://developer.android.google.cn/guide/topics/ui/look-and-feel/themes)

[推荐开发者使用 Material Design 组件（Android开发者）](https://segmentfault.com/a/1190000040483182)

[MaterialPalette颜色配置面板](https://www.materialpalette.com)

[为Android设置Material Components主题](https://www.jianshu.com/p/533b397c63f0)
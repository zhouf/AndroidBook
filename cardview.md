# CardView Demo

> 目标：
>
> 了解CardView基本布局方法
>
> 学习Fragment的加载，以及SeekBar的使用

最终完成效果如下图所示

![](/assets/cardview.png)

### 创建工程

创建一新工程`MyCardView`，包名可以用默认值，也可以修改

初始Activity名称可取`CardViewActivity`，对应生成布局文件`activity_card_view.xml`

### 准备资源文件

在values下面创建资源文件`dimens.xml`，写入布局相关设置
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <dimen name="activity_horizontal_margin">16dp</dimen>
    <dimen name="activity_vertical_margin">16dp</dimen>
    <dimen name="seekbar_label_length">70dp</dimen>
    <dimen name="margin_tiny">4dp</dimen>
    <dimen name="margin_small">8dp</dimen>
    <dimen name="margin_medium">16dp</dimen>
    <dimen name="margin_large">32dp</dimen>
    <dimen name="margin_huge">64dp</dimen>
    <!-- Semantic definitions -->
    <dimen name="horizontal_page_margin">@dimen/margin_medium</dimen>
    <dimen name="vertical_page_margin">@dimen/margin_medium</dimen>
</resources>
```

修改字符串资源`strings.xml`并添加如下内容
```
<resources>
    <string name="app_name">CardView</string>
    <string name="cardview_contents">This is a CardView widget. CardView widgets can have shadows and rounded corners.
        \n\nTo create a card with a shadow, use the <font fgcolor="#FFFFFFFF">android:elevation</font>
        attribute.
        \n\nTo set the corner radius in your layouts, use the <font fgcolor="#FFFFFFFF">card_view:cardCornerRadius</font> attribute.
    </string>
    <string name="cardview_radius_seekbar_text">Radius</string>
    <string name="cardview_elevation_seekbar_text">Elevation</string>
</resources>
```

### 创建Fragment

通过向导创建fragment，取名为`CardViewFragment`，并生成布局文件`fragment_card_view.xml`，布局文件内容如下
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:layout_height="match_parent"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    >

    <androidx.cardview.widget.CardView
        android:id="@+id/cardview"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:elevation="100dp"
        card_view:cardBackgroundColor="@color/cardview_initial_background"
        card_view:cardCornerRadius="8dp"
        android:layout_marginLeft="@dimen/margin_large"
        android:layout_marginRight="@dimen/margin_large"
        >

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="@dimen/margin_medium"
            android:text="@string/cardview_contents"
            />
    </androidx.cardview.widget.CardView>

    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="@dimen/margin_large"
        android:orientation="horizontal"
        >
        <TextView
            android:layout_width="@dimen/seekbar_label_length"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:text="@string/cardview_radius_seekbar_text"
            />
        <SeekBar
            android:id="@+id/cardview_radius_seekbar"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_margin="@dimen/margin_medium"
            />
    </LinearLayout>

    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        >
        <TextView
            android:layout_width="@dimen/seekbar_label_length"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:text="@string/cardview_elevation_seekbar_text"
            />
        <SeekBar
            android:id="@+id/cardview_elevation_seekbar"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_margin="@dimen/margin_medium"
            />
    </LinearLayout>
</LinearLayout>
```

在`CardViewFragment`文件中实现了主要的功能，其代码如下
```
package com.zhouf.mycard;

import android.os.Bundle;
import androidx.cardview.widget.CardView;
import androidx.fragment.app.Fragment;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.SeekBar;

/**
 * A simple {@link Fragment} subclass.
 * Use the {@link CardViewFragment#newInstance} factory method to
 * create an instance of this fragment.
 */
public class CardViewFragment extends Fragment {

    private static final String TAG = CardViewFragment.class.getSimpleName();

    /** The CardView widget. */
    //@VisibleForTesting
    CardView mCardView;

    /**
     * SeekBar that changes the cornerRadius attribute for the {@link #mCardView} widget.
     */
    //@VisibleForTesting
    SeekBar mRadiusSeekBar;

    /**
     * SeekBar that changes the Elevation attribute for the {@link #mCardView} widget.
     */
    //@VisibleForTesting
    SeekBar mElevationSeekBar;

    /**
     * Use this factory method to create a new instance of
     * this fragment using the provided parameters.
     *
     * @return A new instance of fragment NotificationFragment.
     */
    public static CardViewFragment newInstance() {
        CardViewFragment fragment = new CardViewFragment();
        fragment.setRetainInstance(true);
        return fragment;
    }

    public CardViewFragment() {
        // Required empty public constructor
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.fragment_card_view, container, false);
    }

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        mCardView = (CardView) view.findViewById(R.id.cardview);
        mRadiusSeekBar = (SeekBar) view.findViewById(R.id.cardview_radius_seekbar);
        mRadiusSeekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                Log.d(TAG, String.format("SeekBar Radius progress : %d", progress));
                mCardView.setRadius(progress);
            }
            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {
                //Do nothing
            }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                //Do nothing
            }
        });

        mElevationSeekBar = (SeekBar) view.findViewById(R.id.cardview_elevation_seekbar);
        mElevationSeekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                Log.d(TAG, String.format("SeekBar Elevation progress : %d", progress));
                mCardView.setElevation(progress);
            }
            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {
                //Do nothing
            }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                //Do nothing
            }
        });

        //set init value
        mRadiusSeekBar.setProgress(8);
        mElevationSeekBar.setProgress(5);
    }
}
```

### 修改Activity

在布局文件`activity_card_view.xml`中保留一个根布局
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    tools:context="com.zhouf.mycard.CardViewActivity"
    tools:ignore="MergeRootFrame" />
```
修改`CardViewActivity.java`文件中的`onCreate`方法，加载`CardViewFragment`，代码如下
```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_card_view);
    if (savedInstanceState == null) {
        getSupportFragmentManager().beginTransaction().add(R.id.container,CardViewFragment.newInstance()).commit();
    }
}
```

### 加入引用库

修改`build.gradle`添加`cardview`的引用
```
implementation 'androidx.cardview:cardview:1.0.0'
```

### 注意事项

文中所使用的`Fragment`为AndroidX包下的`androidx.fragment.app.Fragment`
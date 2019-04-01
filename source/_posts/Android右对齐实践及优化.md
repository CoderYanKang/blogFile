---
layout: Android
title: Android右对齐布局实践及优化
date: 2017-02-07 12:19:47
categories: 
- Android
tags: UI

---

回想起去年做过的一个维语音乐平台，要求实现双语版本，汉语和维语。在维语版本中要求文字、控件，右对齐显示，我们都知道大部分语言都是左对齐，<!--more-->android默认的对齐方式也是左对齐方式。而像维语和阿拉伯语这种语言都是右对齐的，即是从右往左读的。
  拿到这个问题我首先想到到的是自定义ViewGroup。233333~
  其实从Android 4.2 (api 17)开始 对rtl 提供了相应的支持。

项目中的实际效果

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1959088-ed415ebf59a49f87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那我们来试一下吧~
#### 相关配置
1. minsdk 大于17
2. manifest 文件中添加  android:supportsRtl="true"
3. 使用  
```
<ImageView
    android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:scaleX="-1" //反向
	android:src="@drawable/ic_record_voice_over_blue_900_36dp" />
```
 
#### 简单使用
- ViewGroup 设置 android:layoutDirection="rtl 或 trl";
- TextView 内文字对齐方式 android:textAlignmen = "textEnd 或 textStart" 或  android:textDirection="rtl 或 ltr"

#### 特别的控件

imageview 右对齐

	
viewpager 与 tablayout 右对齐，要求至右往左滑动

```
public class RTLViewPager extends ViewPager {

    // direction
    private boolean mIsRtlOriented;

    public RTLViewPager(Context context){
        this(context, null);
    }
    public RTLViewPager(Context context, AttributeSet attrs){
        super(context, attrs);
    }

    //rtl 时设置当前页码  自定义属性修改更好
    public void setRtlOriented(boolean isRtlOriented){
        mIsRtlOriented = isRtlOriented;
        if(mIsRtlOriented && getAdapter() != null){
            setCurrentItem(getAdapter().getCount() - 1 , false);
        }else{
            setCurrentItem(0 , false);
        }
    }
}

```

设置 adapter 数据反置

```
public class CommonPagerAdapter extends FragmentStatePagerAdapter {
    private final List<Fragment> mFragments = new ArrayList<>();
    private final List<String> mFragmentsTitles = new ArrayList<>();
    private boolean mIsRtrl;

    public CommonPagerAdapter(FragmentManager fm, boolean mIsRtrl) {
        super(fm);
        this.mIsRtrl = mIsRtrl;
    }

    public void addFragment(Fragment fragment, String title) {
        mFragments.add(fragment);
        mFragmentsTitles.add(title);
    }

    public void clearFragment() {
        mFragments.clear();
        mFragmentsTitles.clear();
    }

    @Override
    public Fragment getItem(int position) {
        if (mIsRtrl && mFragments.size() > 0) {
            return mFragments.get(mFragments.size() - position - 1);
        } else {
            return mFragments.get(position);
        }
    }

    @Override
    public int getCount() {
        return mFragments.size();
    }

    @Override
    public CharSequence getPageTitle(int position) {
        if (mIsRtrl && mFragmentsTitles.size() > 0) {
            return mFragmentsTitles.get(mFragmentsTitles.size() - position - 1);
        } else {
            return mFragmentsTitles.get(position);
        }
    }
}
```

#### 完整布局代码

汉语布局  activity_main.xml

```
<?xml version="1.0"encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
	android:id="@+id/activity_main"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:orientation="vertical">
	
	<LinearLayout
		android:layout_width="match_parent"
		android:layout_height="wrap_content"
		android:background="#ffffff"
		android:orientation="horizontal">
		
		<android.support.design.widget.TabLayout
			android:id="@+id/act_tab"
			android:layout_width="wrap_content"
			android:layout_height="48dp"
			app:layout_scrollFlags="scroll"
			app:tabIndicatorColor="@color/colorPrimary"
			app:tabIndicatorHeight="2dp"
			app:tabMode="scrollable"
			app:tabSelectedTextColor="#666666"
			app:tabTextColor="@android:color/darker_gray"></android.support.design.widget.TabLayout>
	
	</LinearLayout>
	
	<com.yankang.rtldemo.RTLViewPager
		android:id="@+id/act_viewpager"
		android:layout_width="match_parent"
		android:layout_height="0dp"
		android:layout_weight="1"></com.yankang.rtldemo.RTLViewPager>
	
	
	<Button
		android:id="@+id/change_lang"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:text="@string/change_lang" />

</LinearLayout>
```

维语布局  activity_main.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
	android:id="@+id/activity_main"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:layoutDirection="rtl"
	android:orientation="vertical">
	
	<LinearLayout
		android:layout_width="match_parent"
		android:layout_height="wrap_content"
		android:background="#ffffff"
		android:orientation="horizontal">
		
		<android.support.design.widget.TabLayout
			android:id="@+id/act_tab"
			android:layout_width="wrap_content"
			android:layout_height="48dp"
			android:layoutDirection="ltr"
			app:layout_scrollFlags="scroll"
			app:tabIndicatorColor="@color/colorPrimary"
			app:tabIndicatorHeight="2dp"
			app:tabMode="scrollable"
			app:tabSelectedTextColor="#666666"
			app:tabTextColor="@android:color/darker_gray"></android.support.design.widget.TabLayout>
	
	</LinearLayout>
	
	<com.yankang.rtldemo.RTLViewPager
		android:id="@+id/act_viewpager"
		android:layout_width="match_parent"
		android:layout_height="0dp"
		android:layout_weight="1"></com.yankang.rtldemo.RTLViewPager>
	
	
	<Button
		android:id="@+id/change_lang"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:text="@string/change_lang" />

</LinearLayout>
```

文件夹结构（rtl布局我放在 layout_en_rUS 下）
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1959088-55449dac07153a1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[以上源码地址](https://github.com/CoderYanKang/AndroidRtlLayout)

---
好吧， 到这里我们基本实现了双语版本 ， 以及版本的切换。
但是 ，我们layout 多了一份 ， String.xml多了一份（这个无法避免），一方面需要同时维护两套布局，布局文件很多的情况下很不方便，另一方面也增大了app的大小。
### 布局优化

使用databinding 简化布局
App字符串常量类 StringVaule.java



```
package com.yankang.rtldemo.Strings;

import android.databinding.BaseObservable;
import android.databinding.Bindable;

import com.yankang.rtldemo.BR;

/**
 * Created by yankang on 2017/1/8.
 * app 字符串常量类
 */

public class StringVaule extends BaseObservable {
    private boolean langType = false;

    @Bindable
    public boolean isLangType() {
        return langType;
    }

    public void setLangType(boolean langType) {
        this.langType = langType;
        notifyPropertyChanged(BR.langType);
    }

    // 文字
    public final String btn = "改变";
    public final String _btn = "change";
}
```



activity_main.xml




```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    
	<data>
		
		<import type="com.yankang.rtldemo.Strings.StringVaule" />
		
		<import type="android.view.View" />
		
		<variable
			name="string"
			type="StringVaule" />
	</data>
	
	<LinearLayout xmlns:app="http://schemas.android.com/apk/res-auto"
		android:id="@+id/activity_main"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:layoutDirection="@{string.langType ? View.LAYOUT_DIRECTION_RTL : View.LAYOUT_DIRECTION_LTR}"
		android:orientation="vertical">
		
		<LinearLayout
			android:layout_width="match_parent"
			android:layout_height="wrap_content"
			android:background="#ffffff"
			android:orientation="horizontal">
			
			<android.support.design.widget.TabLayout
				android:id="@+id/act_tab"
				android:layout_width="wrap_content"
				android:layout_height="48dp"
				android:layoutDirection="ltr"
				app:layout_scrollFlags="scroll"
				app:tabIndicatorColor="@color/colorPrimary"
				app:tabIndicatorHeight="2dp"
				app:tabMode="scrollable"
				app:tabSelectedTextColor="#666666"
				app:tabTextColor="@android:color/darker_gray"></android.support.design.widget.TabLayout>
		
		</LinearLayout>
		
		<com.yankang.rtldemo.Widgets.RTLViewPager
			android:id="@+id/act_viewpager"
			android:layout_width="match_parent"
			android:layout_height="0dp"
			android:layout_weight="1"></com.yankang.rtldemo.Widgets.RTLViewPager>
		
		<Button
			android:id="@+id/change_lang"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:text="@{string.langType ? string._btn : string.btn}" />
	
	</LinearLayout>
</layout>
```



RecycleView item list_item.xml



```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
	
	<data>
		
		<import type="com.yankang.rtldemo.Strings.StringVaule" />
		
		<import type="com.yankang.rtldemo.Model.Student" />
		
		<variable
			name="string"
			type="StringVaule" />
		
		<variable
			name="stu"
			type="Student" />
	</data>
	
	<LinearLayout
		android:layout_width="match_parent"
		android:layout_height="wrap_content"
		android:orientation="horizontal">
		
		<ImageView
			android:layout_width="50dp"
			android:layout_height="50dp"
			android:layout_margin="15dp"
			android:scaleType="centerCrop"
			android:scaleX="@{string.langType ? -1f : 1f}"
			app:image="@{stu.imageUrl}" />
		
		<LinearLayout
			android:layout_width="0dp"
			android:layout_height="wrap_content"
			android:layout_gravity="center"
			android:layout_weight="1"
			android:orientation="vertical">
			
			<TextView
				android:layout_width="wrap_content"
				android:layout_height="wrap_content"
				android:text="@{stu.name}" />
			
			<TextView
				android:layout_width="wrap_content"
				android:layout_height="wrap_content"
				android:layout_marginTop="10dp"
				android:text="@{stu.gender}" />
		
		</LinearLayout>
		
		<ImageView
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:layout_gravity="center_vertical"
			android:layout_marginEnd="10dp"
			android:scaleX="@{string.langType ? -1f : 1f}"
			android:src="@drawable/ic_keyboard_arrow_right_black_36dp" />
	
	</LinearLayout>

</layout>
```




MainActivity.java





```
package com.yankang.rtldemo1;

import android.content.Intent;
import android.databinding.DataBindingUtil;
import android.os.Bundle;
import android.support.design.widget.TabLayout;
import android.support.v4.app.Fragment;
import android.support.v7.app.AppCompatActivity;

import com.yankang.rtldemo1.Adapters.CommonPagerAdapter;
import com.yankang.rtldemo1.Widgets.RTLViewPager;
import com.yankang.rtldemo1.data.PreferManager;
import com.yankang.rtldemo1.databinding.ActivityMainBinding;

import butterknife.Bind;
import butterknife.ButterKnife;
import butterknife.OnClick;

import static com.yankang.rtldemo1.App.stringVaule;

public class MainActivity extends AppCompatActivity {

    @Bind(R.id.act_tab)
    TabLayout actTab;
    @Bind(R.id.act_viewpager)
    RTLViewPager actViewpager;

    private CommonPagerAdapter adapter;

    private boolean isRtl = false;
    private ActivityMainBinding binding;
    private PreferManager preferManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        ButterKnife.bind(this);
        initView();
    }


    private void initView() {
        preferManager = PreferManager.getInstance(this);
        binding.setString(App.stringVaule);

        isRtl = preferManager.getLangType() == Constant.LANG_EN;
        stringVaule.setLangType(isRtl);

        adapter = new CommonPagerAdapter(getSupportFragmentManager(), isRtl);

        for (int i = 0; i < 3; i++) {
            Fragment fragment = new TestFragment();
            adapter.addFragment(fragment, "标签" + i);
        }

        actTab.setupWithViewPager(actViewpager);
        actViewpager.setAdapter(adapter);
        actViewpager.setRtlOriented(isRtl);

    }

    @OnClick(R.id.change_lang)
    public void change() {
        if (preferManager.getLangType() == Constant.LANG_EN) {
            preferManager.setLangType(Constant.LANG_ZH);

        } else {
            preferManager.setLangType(Constant.LANG_EN);
        }

        startActivity(new Intent(this, MainActivity.class));
        finish();
    }

}
```



RecycleView adapter 配置Databinding CommonAdapter.java




```java
package com.yankang.rtldemo.Adapters;

import android.databinding.DataBindingUtil;
import android.databinding.ViewDataBinding;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

import com.yankang.rtldemo.App;
import com.yankang.rtldemo.BR;
import com.yankang.rtldemo.Model.Student;
import com.yankang.rtldemo.R;

import java.util.ArrayList;


/**
 * Created by yankang on 2017/1/8.
 * MVVM 在recycleview 中的应用
 */

public class CommonAdapter extends RecyclerView.Adapter<CommonAdapter.ViewHoler> {

    private final ArrayList<Student> mData = new ArrayList<>();

    public CommonAdapter(ArrayList<Student> data) {
        mData.addAll(data);
    }

    @Override
    public ViewHoler onCreateViewHolder(ViewGroup parent, int viewType) {
        ViewDataBinding binding = DataBindingUtil.inflate(LayoutInflater
                .from(parent.getContext()), R.layout.list_item, parent, false);
        ViewHoler holder = new ViewHoler(binding.getRoot());
        holder.setViewDataBinding(binding);
        return holder;
    }

    //设置数据
    @Override
    public void onBindViewHolder(ViewHoler holder, int position) {
        //修改绑定数据
        holder.getViewDataBinding().setVariable(BR.stu, mData.get(position));
        holder.getViewDataBinding().setVariable(BR.string, App.stringVaule);

        //刷新
        holder.getViewDataBinding().executePendingBindings();
    }

    @Override
    public int getItemCount() {
        return mData.size();
    }

    class ViewHoler extends RecyclerView.ViewHolder {

        private ViewDataBinding viewDataBinding;

        public ViewHoler(View itemView) {
            super(itemView);
        }

        public ViewDataBinding getViewDataBinding() {
            return viewDataBinding;
        }

        public void setViewDataBinding(ViewDataBinding viewDataBinding) {
            this.viewDataBinding = viewDataBinding;
        }
    }
}

```

[以上源码地址](https://github.com/CoderYanKang/Android_RTL_DataBinding.git)
	
### 实际效果

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1959088-61d0d00622676dc0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
---

使用DataBinding后，我们不用再维护多套布局 ， 极大的简化了代码。总体来说还是很简单的。

end~
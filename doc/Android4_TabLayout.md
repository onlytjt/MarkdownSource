---
title: Android4：使用TabLayout和ViewPager实现顶部标签页
date: 2016-04-14 19:54:34
tags:
 - Android
---

自Android5.0 Lollipop发布以来，Material Design在Android App中爆炸性增长，涌现出了一批优秀的、拥有Android风格的App。今天的主题是使用新的`android.support.design`包中的TabLayout配合ViewPager以及实现经典的Android风格—顶部标签页。

我将Tab加到了我在做的“ijob” app中，先来看一下最终的效果。

![](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android4_1.gif "")

<!-- more -->

#### 创建布局文件

布局文件没有什么好说的，比较简单，注意区分控件所在的不同support library。还想多啰嗦几句。(a) 在Android5.0以后可以轻松地给控件设置阴影效果，`android:elevation="8dp"`。(b) 如果阴影效果没有如期显示，多半是因为忘记给控件设置背景颜色了，其实只要设置了背景颜色，阴影效果就出来了。`android:background="@android:color/white"`

除此之外，为了实现我们想要的效果，我们可以设置TabLayout的一些属性，包括Tab高度、Tab颜色，Tab中Indicator的高度及颜色。

```xml
<!--activity_main.xml-->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    tools:context=".MainActivity">
    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/color_primary"
        android:elevation="8dp"/>
    <android.support.design.widget.TabLayout
        android:id="@+id/tablayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabBackground="@color/color_primary"
        app:tabTextAppearance="@style/MyTabText"
        app:tabIndicatorColor="@android:color/white"
        app:tabIndicatorHeight="3dp"
        android:background="@android:color/white"
        android:elevation="8dp"/>
    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:background="@android:color/white"/>
</LinearLayout>
```

#### 创建ViewPager适配器

![](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android4_2.png)

ViewPager的适配器主要是连接TabLayout和将要显示的多个Fragment。由于ViewPager是在v4的包中，Fragment在v4和android.app的包中都含有，如果发生不对应情况，很容易挂掉。所以，在这里罗列出了相关包的版本建议。

* `import android.app.Fragment;`
* `import android.app.FragmentManager;`
* `import android.support.v13.app.FragmentPagerAdapter;`
* `import android.support.v4.view.ViewPager;`

```java
// MyFragmentPagerAdapter.java
public class MyFragmentPagerAdapter extends FragmentPagerAdapter {
    private final int PAGE_COUNT = 3;
    private String[] tableTitle = new String[] {"宣讲会", "实习", "工作"};
    private Context mContext;
    private List<Fragment> mFragmentTab;
    private SeminarFragment mSeminarFragment;
    private InternFragment mInternFragment;
    private JobFragment mJobFragment;

    public MyFragmentPagerAdapter(FragmentManager fm, Context context) {
        super(fm);
        mContext = context;
        initFragmentTab();
    }

    @Override
    public Fragment getItem(int position) {
        return mFragmentTab.get(position);
    }

    @Override
    public int getCount() {
        return PAGE_COUNT;
    }

    @Override
    public CharSequence getPageTitle(int position) {
        return tableTitle[position];
    }

    private void initFragmentTab() {
        mSeminarFragment = new SeminarFragment();
        mInternFragment = new InternFragment();
        mJobFragment = new JobFragment();
        mFragmentTab = new ArrayList<Fragment>();
        mFragmentTab.add(mSeminarFragment);
        mFragmentTab.add(mInternFragment);
        mFragmentTab.add(mJobFragment);
    }
}
```

#### 设置TabLayout

最后一步就是在我们的MainActivity中对TabLayout进行设置，即把TabLayout控件与Adapter双向关联。

```java
// MainActitity.java
private void initTabLayout() {
	MyFragmentPagerAdapter adapter = new MyFragmentPagerAdapter(getFragmentManager(), this);
    ViewPager viewPager = (ViewPager) findViewById(R.id.viewpager);
    TabLayout tabLayout = (TabLayout) findViewById(R.id.tablayout);
    viewPager.setAdapter(adapter);
    tabLayout.setupWithViewPager(viewPager);
    tabLayout.setTabMode(TabLayout.MODE_FIXED);
    }
```

#### 引用和链接

1. 本文源代码：https://github.com/onlytjt/AndroidApp-ijob
2. http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0731/3247.html
3. Magerial Design新控件：http://www.jcodecraeer.com/a/anzhuokaifa/developer/2015/0531/2958.html
4. Android兼容包详解：http://stormzhang.com/android/2015/03/29/android-support-library/

---

**By tjt**

**2016.04.13**






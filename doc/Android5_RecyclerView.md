---
title: Android5：RecyclerView使用简介
date: 2016-04-15 13:39:16
tags:
 - Android
---

在解决列表显示，使用ListView或者CardView时，程序员自定义ViewHolder来缓存item，减少findViewById的调用，提高效率。这种方案已经基本成为了一种定式。Google在v7的包中推出了新的列表显示方案来代替ListView，这就是RecyclerView。

RecyclerView给我们提供了一个高自由度、高解耦的显示方案。不仅内置了ViewHolder，还集List、Card、以及瀑布流于一身。

<!-- more -->

RecyclerView提供了以下5个类供我们使用：

| 类名                          | 使用说明                           |
| --------------------------- | ------------------------------ |
| RecyclerView.Adapter        | 数据适配器，为每一项Item创建视图并且绑定数据       |
| RecyclerView.ViewHolder     | 承载Item视图的子布局，并进行缓存             |
| RecyclerView.LayoutManager  | 负责Item视图的布局的显示管理               |
| RecyclerView.ItemDecoration | 给每一项Item视图添加子View，例如可以进行画分隔线之类 |
| RecyclerView.ItemAnimator   | 负责处理数据添加或者删除时候的动画效果            |

#### 添加依赖项

使用v7包中的RecyclerView，首先在gradle中添加相应的包依赖项。

```groovy
compile 'com.android.support:recyclerview-v7:23.3.0'
compile 'com.android.support:cardview-v7:23.3.0'
```

#### 创建布局

首先要做的当然是给我们需要使用的控件一个合适的布局文件。与ListView一样，我们的布局文件包括两个，一个是RecyclerView本身，另外一个便是List中的item项。

```xml
<!-- activity_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</RelativeLayout>
```

```xml
<!-- list_item.xml -->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" 		
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/text_view_list"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:gravity="center"
        android:text="@string/test"/>
</LinearLayout>
```

#### 自定义适配器

![](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android5_1.png)	

适配器继承于RecyclerView.Adapter，必须要重写的是以下三个函数：

* onCreateViewHolder()，绑定item的布局文件
* onBindViewHolder()，将数据与item关联
* getItemCount()，返回需要显示的item个数

```java
/* MyAdapter.java */
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {
    private String[] mData;
    public MyAdapter(String[] data) {
        this.mData = data;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.list_item, parent, false);
        ViewHolder vh = new ViewHolder(v);
        return vh;
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        holder.mTextView.setText(mData[position]);
    }

    @Override
    public int getItemCount() {
        return mData.length;
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {
        public TextView mTextView;
        public ViewHolder(View v) {
            super(v);
            mTextView = (TextView) v.findViewById(R.id.text_view_list);
        }
    }
}
```

#### 添加使用RecyclerView

最后一步便是在Activity中初始化并使用。对于RecyclerView不仅需要绑定数据适配器，还要为其绑定一个LayoutManager。这里为了显示列表，使用了最简单的LinearLayoutManager。除此之外还有用于实现网格视图的GridLayoutManager，以及用于实现瀑布流的StaggeredGridLayoutManager。

```java
/* MainActivity.java */
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;

public class MainActivity extends AppCompatActivity {
    private RecyclerView mRecyclerView;
    private LinearLayoutManager mLayoutManager;
    private MyAdapter mAdapter;
    private String[] mData;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initRecyclerView();
    }

    private void initRecyclerView() {
        getData();
        mRecyclerView = (RecyclerView) findViewById(R.id.recycler_view);
        mLayoutManager = new LinearLayoutManager(this);
        mAdapter = new MyAdapter(mData);

        mRecyclerView.setLayoutManager(mLayoutManager);
        mRecyclerView.setAdapter(mAdapter);
      mRecyclerView.addItemDecoration(new DividerItemDecoration(this,
                DividerItemDecoration.VERTICAL_LIST));  // 设置分隔线 
    }

    private void getData() {
        mData = new String[30];
        for (int i=0; i<mData.length; i++) {
            mData[i] = "This is " + i + " item";
        }
    }
}
```

完成以上步骤，最最简单的RecyclerView就能够运行了。

![](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android5_2.png)

#### 设置分隔线

虽然RecyclerView给了我们高度的定制自由，但也给我带来了一些工作量。比如没有默认的分隔线，没有默认的点击消息响应。

这里借鉴使用了github上的一个Divider实现类。[alexfu/DividerItemDecoration.java](https://gist.github.com/alexfu/0f464fc3742f134ccd1e)

该类的实现是读取系统theme中的`android.R.attr.listDivider`作为Item间的分隔线。因此我们如果想要自己定制的话，可以新建自己的theme，并自定义一个Drawable作为分隔线。

```xml
<!-- divider.xml -->
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <size android:height="2dp"/>
    <solid android:color="@android:color/black"/>
</shape>
```

```xml
<!-- my_theme.xml -->
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="MyTheme" parent="Theme.AppCompat.Light">
        <item name="android:listDivider">@drawable/divider</item>
    </style>
</resources>
```

![](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android5_3.png)

#### 下阶段任务

[ ] 瀑布流实现

[ ] RecyclerView的单击响应

#### 引用和链接

1. 本文源码：https://github.com/onlytjt/MarkdownSource/tree/master/example/TestAppBarLayout
2. https://github.com/baoyongzhang/RecyclerViewDemo/wiki/Introduce
3. http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2004.html

---

**By tjt**

**2016.04.15**
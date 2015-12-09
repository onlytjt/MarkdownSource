### Android入门2：从GridView控件使用到自定义Adapter

在日常手机app的使用中，出现频率最高的便是ListView和GridView。ListView的例子是微信主界面，而GridView的例子则是支付宝的主界面，不明白的小伙伴打开手机便一目了然。然而这两个控件同时也称成为最难使用的控件，由于两个控件使用方法完全相同，今天博主以GridView为例，讲一讲我和它的故事。

#### 1. 小白初探--使用SimpleAdapter加载本地资源

单纯学习使用GridView还是很简单的，只需要使用一个数据适配器将所需要显示的数据资源(包括图像和文本)，与布局文件中的控件连接起来即可。

![Android2_1](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android2_1.png "数据适配器")

``` xml
// activity_gridview.xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:paddingLeft="@dimen/activity_horizontal_margin"
                android:paddingRight="@dimen/activity_horizontal_margin"
                android:paddingTop="@dimen/activity_vertical_margin"
                android:paddingBottom="@dimen/activity_vertical_margin"
                tools:context="edu.sjtu.helloworld.app.GridviewActivity"
                android:id="@+id/grid">
    <GridView
            android:id="@+id/grid_test"
            android:numColumns="auto_fit"
            android:horizontalSpacing="10dp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content">
    </GridView>

</RelativeLayout>
```

``` xml
// grid_item.xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:gravity="center"
              android:background="#ffffff"
              android:layout_width="match_parent"
              android:layout_height="match_parent">
    <ImageView
            android:id="@+id/img_item"
            android:src="@mipmap/ic_launcher"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>

    <TextView
            android:id="@+id/txt_item"
            android:textColor="#000000"
            android:text="文字区域"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>

</LinearLayout>
```

``` java
// GridViewActivity.java
package edu.sjtu.helloworld.app;

import android.content.Intent;
import android.net.Uri;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class GridviewActivity extends AppCompatActivity {

    private GridView grid_test;
    private List<Map<String, Object>> dataList;
    private SimpleAdapter simpAdapter;
    private int[] img = {R.mipmap.conan, R.mipmap.death_note,
        R.mipmap.naruto, R.mipmap.one_piece};
    private String[] imgName = {"conan", "death_note", "naruto", "one_piece"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_gridview);

        grid_test = (GridView) findViewById(R.id.grid_test); // step1
        dataList = new ArrayList<Map<String, Object>>(); // step2
        simpAdapter = new SimpleAdapter(this, getData(), R.layout.grid_item, 
            new String[]{"img", "txt"}, new int[]{R.id.img_item, R.id.txt_item});
        grid_test.setAdapter(simpAdapter); // step3
    }

    private List<Map<String, Object>> getData() {
        for (int i=0; i<img.length; i++) {
            Map<String, Object> map = new HashMap<String, Object>();
            map.put("img", img[i]);
            map.put("txt", imgName[i]);
            dataList.add(map);
        }
        return dataList;
    }
}
```

以上代码实现GridView主要分为3步。  

step1：将GridView控件与xml连接。  

step2：根据xml中想要显示的数据格式，初始化数据源，并使用`getData()`函数把本地数据添加到数据源中。这里的数据是一个List，List中每一条对应GridView的一块区域(也就是ListView中的一行)。List中存放的是一个Map泛型，其中的数据与想要显示的内容一一对应。  

step3：给GirdView设置相应的数据适配器。  

经过以上3步，可以很简单地把自己想要显示的本地资源，静态加载出来。效果如下图。

![Android2_2](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android2_2.png "GridView静态加载本地资源效果")



#### 2. 招式进阶--定制自己的数据适配器Adapter

当我们需要处理较为复杂的界面，加载接收多种信息时，Android提供给我们的数据适配器往往会显得苍白无力。因此我们会根据自己的需要定义数据适配器。xml文件布局同上。  

首先看一下Android中所有有关Adapter的接口和类的继承关系。我们选择继承ArrayAdapter来写我们自己的适配器。

![Android2_3](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android2_3.jpg "Adapter适配器继承关系")



``` java
class MyAdapter extends ArrayAdapter<GridItem> {
    private Context mContext;
    public MyAdapter(Context context) {
    	this.context = context;
    }
    @Override
    public int getCount() {
        return 0; // 返回Adapter中数据集的条目数
    }
    @Override
    public Object getItem(int position) {
        return null; // 获取数据集中与指定索引对应的数据项
    }
    @Override
    public long getItemId(int position) {
        return 0; // 取在列表中与指定索引对应的行id
    }
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        return null;
    }
}
```

``` java
// GridItem.java
package cn.edu.sjtu.videoplayer.bannedplayer02;

public class GridItem {
    private String image;
    private String title;

    public GridItem() {
        super();
    }
    public String getImage() {
        return image;
    }
    public void setImage(String image) {
        this.image = image;
    }
    public String getTitle() {
        return title;
    }
    public void setTitle(String title) {
        this.title = title;
    }
}
```

``` java
// GridViewAdapter.java
package cn.edu.sjtu.videoplayer.bannedplayer02;

import android.app.Activity;
import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.ImageView;
import android.widget.TextView;
import com.squareup.picasso.Picasso;

import java.util.ArrayList;

/**
 * Created by tian on 12/7/15.
 */
public class GridViewAdapter extends ArrayAdapter<GridItem> {

    private Context mContext;
    private int layoutResourceId;
    private ArrayList<GridItem> mGridData = new ArrayList<GridItem>();


    public GridViewAdapter(Context context, int resource, ArrayList<GridItem> objects) {
        super(context, resource, objects);
        this.mContext = context;
        this.layoutResourceId = resource;
        this.mGridData = objects;
    }

    public void setGridData(ArrayList<GridItem> mGridData) {
        this.mGridData = mGridData;
        notifyDataSetChanged();
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        ViewHolder holder;

        if (convertView == null) {
            LayoutInflater inflater = ((Activity)mContext).getLayoutInflater();
            convertView = inflater.inflate(layoutResourceId, parent, false);
            holder = new ViewHolder();
            holder.textView = (TextView) convertView.findViewById(R.id.textview_item);
            holder.imageView = (ImageView) convertView.findViewById(R.id.imgview_item);
            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }
        GridItem item = mGridData.get(position);
        holder.textView.setText(item.getTitle());
        Picasso.with(mContext).load(item.getImage()).into(holder.imageView);
        return convertView;
    }

    private class ViewHolder {
        TextView textView;
        ImageView imageView;
    }
}
```

这里需要重点讲也是必须重写的是`getView()`方法。首先我们重复使用缓存convertView，如果缓存区已经存在View对象，我们则不再重新创建。同时，我们也使用ViewHolder对实例化后的控件进行暂存，因为`findViewById`方法的查找是很费内存的。如果一个Item中有许多内容，每次的查找过程将耗费我们众多的时间。虽然我还是小白一个，但是一定要有一颗大神的心，代码要写效率高的(笑)。  

在这里我其实是有一些疑问的，在这个类的代码中，我们看到了控件和Adapter的绑定过程，也看到了Adapter向控件(TextView, ImageView)写入我们的资源，但是并没有看到将数据源设置到Adapter的具体代码。关于这个问题，大家可以在文章最后附上的一些链接中找寻答案，也可以打开源码自己分析。鉴于篇幅问题，本篇文章暂不对此问题进行深入探讨，后面我应该会写一遍关于Adapter实现机理和源码分析的blog，嘛，都是后话了。在对ImageView的设置中，为了简化代码，这里我使用了一个第三方库--Picasso，用于下载并缓存网络图片。这也不是本文重点，这几天我同时也在针对网络的异步请求准备材料，相关的文章很快就能写出来。  

``` java
package cn.edu.sjtu.videoplayer.bannedplayer02;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.media.Image;
import android.os.AsyncTask;
import android.os.Handler;
import android.os.Message;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.GridView;
import android.widget.ImageView;
import android.widget.SimpleAdapter;

import java.io.InputStream;
import java.lang.ref.WeakReference;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.*;

public class MainActivity extends AppCompatActivity {

    private String[] localCartoonText = {"名侦探柯南", "死亡笔记", "火影忍者", "海贼王"};
    private String[] imgURL ={"http://img1.imgtn.bdimg.com/it/u=3350993068,3652800343&fm=21&gp=0.jpg", "http://img0.imgtn.bdimg.com/it/u=2545030489,2226096219&fm=21&gp=0.jpg", "http://img3.imgtn.bdimg.com/it/u=3171772449,1023293196&fm=21&gp=0.jpg", "http://img0.imgtn.bdimg.com/it/u=820734872,552500686&fm=21&gp=0.jpg"};
    private GridView mGridView = null;
    private GridViewAdapter mGridViewAdapter = null;
    private ArrayList<GridItem> mGridData = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mGridView = (GridView) findViewById(R.id.gridview_cartoon);
        mGridData = new ArrayList<GridItem>();
        for (int i=0; i<imgURL.length; i++) {
            GridItem item = new GridItem();
            item.setTitle(localCartoonText[i]);
            item.setImage(imgURL[i]);
            mGridData.add(item);
        }
        mGridViewAdapter = new GridViewAdapter(this, R.layout.grid_item, mGridData);
        mGridView.setAdapter(mGridViewAdapter);
    }
}
```

我们在MainActivity中给数据适配器添加数据源，并绑定GridView，就像我们在第一小节所讲的步骤一样。另，如果想使用Picasso，在使用gradle管理的项目中，只需要在`build.gradle`文件中添加相关依赖就可以了。

``` groovy
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:23.1.1'
    compile 'com.squareup.picasso:picasso:2.5.2'
}
```

最后附上效果，经过短暂的下载，GridView可以成功使用相关图片的uri进行下载并显示。博主都是使用真机调试，所以这个屏幕截图可能看起来比较大，有点影响博客效果，也希望同志们见谅~下次再写blog时，我使用Genymotion给大家截图:)

![Android2_4](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android2_4.png "效果显示")



##### 3. Reference

(1) ViewHolder详解：http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1021/1815.html  

(2) inflate方法与findViewById：http://www.cnblogs.com/loyea/archive/2013/04/27/3047248.html  

(3) http://javatechig.com/android/download-and-display-image-in-android-gridview  

***

**By tjt**

**2015.12.09**




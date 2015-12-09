###Android入门2：从GridView控件使用到自定义Adapter

在日常手机app的使用中，出现频率最高的便是ListView和GridView。ListView的例子是微信主界面，而GridView的例子则是支付宝的主界面，不明白的小伙伴打开手机便一目了然。然而这两个控件同时也被成为最难使用的两个控件，由于两个控件使用方法完全相同，今天博主以GridView为例，讲一讲我和它的故事。

#####1. 小白初探--使用SimpleAdapter加载本地资源
单纯学习使用GridView还是很简单的，只需要使用一个数据适配器将所需要显示的数据资源(包括图像和文本)，与布局文件中的控件连接起来即可。
![数据适配器](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android2_1.png)

```xml
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

```xml
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

```java
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

经过以上3步，可以很简单地把自己想要显示的本地资源，静态加载出来。

#####2. 招式进阶--定制自己的数据适配器Adapter
当我们需要处理较为复杂的界面，加载接收多种信息时，Android提供给我们的数据适配器往往会显得苍白无力。因此我们会根据自己的需要定义数据适配器。xml文件布局同上。  

首先看一下Android中所有有关Adapter的接口和类的继承关系。我们选择继承ArrayAdapter来写我们自己的适配器。

```java
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









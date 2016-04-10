### 从Android Toolbar到Material Design

在Android5.0(API 21)之后，Toolbar被Google推广，逐渐走入大家视野。具体关于Actionbar和Toolbar的对比就不多啰嗦了，跟着潮流走是没错的。下面先上张简单的效果图，这是我近期在构思的一个app，帮助我们搜集宣讲会、实习、工作信息。

![Android Toolbar效果图](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android3_1.png)

####1. 初识Material Theme

![Material Theme](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android3_2.png)

为了使用Material Design相关资源，新建工程时需要注意Min SDK需要大于等于API21。我们先来了解一下Material Theme。如上图所示，可以进行设置的部分有：

* colorPrimary：Toolbar的主体颜色
* colorPrimaryDark：Toolbar上方的背景颜色，一般为深色，会是的app看起来更有层次感
* textColorPrimary：Toolbar的title文字颜色

剩下的参数也很明了了。这里所有的属性后面都跟了一个"Primary"是因为，Toolbar不仅可以在xml中出现，还能够在view中进行任意的设置，而且还可以将其当做一个Actionbar来使用~

###2. 自定义Theme

在`/res/values/`文件夹下我们需要新建3个xml文件

* my_theme.xml：用于自定义主题，配置主题中我们所需要的颜色
* default_colors_setting.xml：将颜色建立别名，方便引用和修改
* android_material_design_colors.xml：调色板，用于存储一些色彩

关于android_material_design_colors.xml，我直接下载了google给出的色彩调色方案，添加到工程中，可以直接引用其中的颜色。下载地址：https://gist.github.com/daniellevass/b0b8cfa773488e138037。另外有一个比较不错的网站，[Material Palette](https://gist.github.com/daniellevass/b0b8cfa773488e138037) 可以在线预览我们的颜色搭配，小伙伴们不妨都去试试，能给设计省下不少时间~

```xml
/res/values/default_colors_setting.mxl
<?xml version="1.0" encoding="utf-8"?>
<!-- 主要目的是给常用颜色设置别名，方便引用和更改 -->
<resources>
    <color name="color_primary">@color/md_light_blue_400</color>
    <color name="color_primary_dark">@color/md_light_blue_700</color>
    <color name="color_primary_text">@android:color/white</color>
</resources>
```

```xml
/res/values/my_theme.xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="MyMaterialTheme" parent="MyMaterialTheme.Base">
    </style>

    <style name="MyMaterialTheme.Base" parent="Theme.AppCompat">
      	<!-- 移除Actionbar，以下两句缺一不可 -->
        <item name="windowNoTitle">true</item>
        <item name="windowActionBar">false</item>
        <!-- Toolbar主体颜色 -->
        <item name="android:colorPrimary">@color/color_primary</item>
        <!-- Toolbar上方的背景颜色,一般比主体颜色深 -->
        <item name="android:colorPrimaryDark">@color/color_primary_dark</item>
        <!-- 其他可以使用的属性参数:colorAccent, colorControlNormal,
        colorControlActivated, colorControlHighlight, colorSwitchThumbNormal -->
    </style>
</resources>
```

完成了我们自己的Theme，接下来我们要在app中使用我们的Theme，修改`/res/AndroidManifest.xml`

```xml
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/MyMaterialTheme" >
......
```

此时运行程序，便可以看到我们自定义的主题已经生效，但此时app的上方还是空空如也，应为我们还没有为其添加Toolbar。

![](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android3_3.png)

#### 3. 添加Toolbar

首先我们要给Toolbal新建一个布局文件，/res/layout/toolbar.xml。这里使用v7包中的Toolbar。使用gradle的同学记得添加依赖。`compile 'com.android.support:appcompat-v7:23.1.1'`

```xml
<?xml version="1.0" encoding="utf-8"?>
<android:android.support.v7.widget.Toolbar
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/toolbar"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:minHeight="?attr/actionBarSize"
        android:background="@color/color_primary"
/>
```

然后我们在主布局文件中引入toolbar，/res/layout/activity_main.xml

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical"
                tools:context=".MainActivity">
    <include
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        layout="@layout/toolbar"/>

    <TextView
            android:text="@string/hello_world"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@+id/toolbar"
    />
</LinearLayout>
```

最后，我们在`onCreate()`中进行设置。

```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

      	private Toolbar mToolbar;
    	private ActionBar mActionbar;

        mToolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(mToolbar);
        mActionbar = getSupportActionBar();  // 转换为Actionbar操作
        mActionbar.setTitle("爱实习  爱工作");  // 设置toolbar标题
      	// 设置toolbar左侧icon，也成为Navigation Icon
        mToolbar.setNavigationIcon(R.mipmap.ic_menu_white_36dp); 
    }
```

完成以上步骤就可以得到最终的效果图啦。

这里还想推荐给小伙伴的是github上的一个开源项目：[mikepenz/Android-Iconics](https://github.com/mikepenz/Android-Iconics)。这里有许许多多基于Material Design的icon，貌似这是个大神~他的SlideMenu的插件也很好用。希望能给小伙伴省下一些寻找资源的时间，开发更加顺利。

![](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Android3_4.jpg)

***

**By tjt**

**2016.03.14**
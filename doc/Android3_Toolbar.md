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

此时运行程序，便可以看到我们自定义的主题已经生效，但此时






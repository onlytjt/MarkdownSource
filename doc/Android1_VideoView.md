###Android入门1：使用VideoView和MediController播放视频

最近在搞Android，入门曲线还是挺陡峭的，主要还是自己对Java的理解不够深入。前后搞了一周多了，把最近学习到的一些知识点总结归纳一下，正所谓温故而知新。

目前在搞一个禁播视频站，主要内容都是一些大陆被墙的动漫啊，美剧啥的，比如我最爱的死亡笔记。内容不包含18禁的~感觉有些小伙伴要失望咯。

#####1. Android入门素材
以下罗列在我学习过程中使用的感觉还算不错的素材  
a. Official Android Training：developer.android.com/training/index.html  
b. 上述Android官方教程中文翻译版本：http://hukai.me/android-training-course-in-chinese/index.html  
c. imooc网站[Android攻城狮入门系列]：http://www.imooc.com/index/search?words=android  
d.《第一行代码-Android》pdf我放到自己的百度网盘里面，希望大家不要去告诉作者(笑)
链接: http://pan.baidu.com/s/1sjy3rw5  密码: 7gy6

#####2. VideoView和MediaController介绍
Android VideoView类为我们提供了十分方便的视频播放API，其主要方法有。

```java
void start()：开始播放
void stopPlayback()：停止播放
void pause()：暂停
void resume()：重新播放
void seekTo(int msec)：从第几毫秒开始播放
int getCurrentPosition()：获取当前播放的位置
int getDuration()：获取当前播放视频的总长度
boolean isPlaying()：当前VideoView是否在播放视频
void setVideoPath(String path)：以文件路径的方式设置VideoView播放的视频源。
void setVideoURI(Uri uri)：以Uri的方式设置视频源，可以是网络Uri或本地Uri。
setMediaController(MediaController controller)：设置MediaController控制器。
setOnCompletionListener(MediaPlayer.onCompletionListener l)：监听播放完成的事件。
setOnErrorListener(MediaPlayer.OnErrorListener l)：监听播放发生错误时候的事件。
setOnPreparedListener(MediaPlayer.OnPreparedListener l)：：监听视频装载完成的事件。
```

除了简单的操作和丰富的API以外，我们在装载视频时，无需进行编码，播放完成后也无需手动回收资源。

除了播放视频，Media Controller类则为我们提供了一个悬浮的操作栏，包含了播放，暂停，快进，快退，上一个，下一个等功能键。甚至连拖动进度条至某处播放都已经实现，简直是业界良心。在使用前VideoView和MediController需要相互指定控件。其内置方法有。

```java
boolean isShowing()：当前悬浮控制栏是否显示。
void setMediaPlayer(MediaController.MediaPlayerControl player)：设置控制的组件。
void setPrevNextListeners(View.OnClickListener next,View.OnClickListener prev)：设置上一个视频、下一个视频的切换事件。
```

默认情况下，Media Controller悬浮显示3s后隐藏，触摸响应的VideoView呼出。默认上一个，下一个按钮隐藏。

#####3.播放视频Demo程序
```java
package cn.edu.sjtu.videoplayer.bannedvideoplayer;

import android.app.ProgressDialog;
import android.content.Intent;
import android.media.MediaPlayer;
import android.net.Uri;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.*;
import java.io.File;

public class MainActivity extends AppCompatActivity {

    private VideoView vv_videoview;
    private MediaController mController;
    private Button btn_play;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btn_play = (Button) findViewById(R.id.btn_play);
        vv_videoview = (VideoView) findViewById(R.id.vv_videoview);
        mController = new MediaController(this);

		// Button响应事件，播放本地视频
        btn_play.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
			// Uri videoUri = Uri.parse("http://www.androidbegin.com/tutorial/AndroidCommercial.3gp");
			// vv_videoview.setVideoURI(videoUri);
                File videoFile = new File("/sdcard/DCIM/Camera/test.mp4");
                if (videoFile.exists()) {
                    vv_videoview.setVideoPath(videoFile.getAbsolutePath());
                    vv_videoview.setMediaController(mController);
                    vv_videoview.start();
                    vv_videoview.requestFocus();
                }
            }
        });
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        int id = item.getItemId();
        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true;
        }
        return super.onOptionsItemSelected(item);
    }
}
```

Xml文件只有简单地一个Button和VideoView控件，节约篇幅就不上了，由于博主一直使用真机Debug，这里就不截图了。如果需要播放网络视频，只需要看我注释的两行，将`setVideoPath()`更改为相应的`setVideoURI()`就可以了。这里需要在AndroidManifest.xml添加两条权限。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.INTERNET" />
```

这里需要多啰嗦的，也是经常会困惑刚入门同志的几个点。  
a. 增加的权限开头android.permission是小写的，Android Studio可能会默认补全为大写，导致没有相应权限。  
b. VideoView虽然可以播放网络视频，但是网络视频流实际是分为Http协议和RTSP(Real Time Streaming Protocal)两种。RTSP协议支持实时播放，而Http协议则需要缓冲一定时间才能够播放。  
c. 国内传统的视频站给出的URL并不是真实视频地址，因此像优酷，爱奇艺等网站的视频并不能直接使用VideoView播放。  

#####4. Reference：
a) http://www.androidbegin.com/tutorial/android-video-streaming-videoview-tutorial/  
b) http://www.cnblogs.com/plokmju/p/android_videoview.html  

***
**By tjt**  
**2015.12.02**







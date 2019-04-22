# MDFullAD
米赋广告SDK开发文档

## 一、接入
下载MDFullAD_\*.aar，放入项目的libs文件夹下。
添加引用。如果是gradle3.0以下版本，在app的build.gradle文件中做如下配置：
```java
repositories {
    flatDir {
        dirs 'libs'
    }
}

dependencies {
    implementation(name: 'MDFullAD_*', ext: 'aar')
}
```
其中\*代表sdk的版本号。如果是gradle3.0以上版本，用下面方式配置：
```java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.aar'])
}
```
如果app需要混淆，请添加以下规则防止sdk二次混淆
```java
-keep class mf.com.adsdk_demo.**
```

请求广告时可能出现网络不通的问题，在manifest文件application节点上添加属性android:usesCleartextTraffic="true"

## 二、使用
### 1、添加权限
在manifest文件中添加必要权限
```java
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_SMS" />
    <uses-permission android:name="android.permission.READ_PHONE_NUMBERS" />
    <uses-permission android:name="android.permission.SEND_SMS" />
    <uses-permission android:name="android.permission.RECEIVE_SMS" />
```
要求安卓api版本最低api19，在app的build.gradle中配置 minSdkVersion 19
### 2、适配API23以上权限配置
添加动态权限申请
```java
        PermissionUtil permissionUtil;
        permissionUtil=new PermissionUtil();
        permissionUtil.requestPermission(this, new String[]{//申请必要的权限
                Manifest.permission.ACCESS_COARSE_LOCATION,
                Manifest.permission.ACCESS_FINE_LOCATION,
                Manifest.permission.ACCESS_NETWORK_STATE,
                Manifest.permission.ACCESS_WIFI_STATE,
                Manifest.permission.READ_PHONE_STATE,
                Manifest.permission.INTERNET,
                Manifest.permission.READ_SMS,
                Manifest.permission.READ_PHONE_NUMBERS,
                Manifest.permission.SEND_SMS,
                Manifest.permission.RECEIVE_SMS,
                Manifest.permission.READ_EXTERNAL_STORAGE,
                Manifest.permission.WRITE_EXTERNAL_STORAGE
        }, 110, new PermissionUtil.Callback() {
            @Override
            public void onResult(int requestCode) {
                System.out.println("权限申请成功");
                //有权限后才能执行下一步
            }
        });
```
注册权限回调
```java
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        permissionUtil.onRequestPermissionsResult(110);//注册权限申请回调
    }
```
### 3、基本使用方法
在请求广告前注册app,参数1:上下文对象，参数2:存放广告位ID的SpaceId对象表。
```java
    Map<String,SpaceId> map=new HashMap<>();//声明一个map存放广告位id
    //把申请到的广告位id放进去，媒体需要向米赋相关人员获取获取各种类型广告位的ID
    map.put(Constant.静态图开屏广告,new SpaceId(MDFullADConstant.静态图开屏广告,"你申请到的静态图开屏广告位id"));
    map.put(Constant.动态图开屏广告,new SpaceId(MDFullADConstant.动态图开屏广告,"你申请到的动态图开屏广告位id"));
    map.put(Constant.视频开屏广告,new SpaceId(MDFullADConstant.视频开屏广告,"你申请到的视频开屏广告位id"));
    map.put(Constant.信息流大图广告,new SpaceId(MDFullADConstant.信息流大图广告,"你申请到的信息流大图广告位id"));
    map.put(Constant.信息流三图广告,new SpaceId(MDFullADConstant.信息流三图广告,"你申请到的信息流三图广告位id"));
    map.put(Constant.图文信息流广告,new SpaceId(MDFullADConstant.图文信息流广告,"你申请到的图文信息流广告位id"));
    map.put(Constant.视频信息流广告,new SpaceId(MDFullADConstant.视频信息流广告,"你申请到的视频信息流广告位id"));
    //通过广告位id注册
    MDFullApp.register(context,map);//注册
```
其中context为上下文对象.
## 三、广告类型
## a、开屏广告
开屏广告由sdk托管，直接调用提供的方法跳转，可以在主界面还未设置数据时直接跳转。开屏广告目前包括：静态图开屏，动态图开屏，视频开屏
### 1、静态开屏广告
```java
    MDFullApp app=new MDFullApp(MainActivity.this);
    //参数1:上下文  参数2:默认显示的logo图  参数3:默认在logo图停留的时间毫秒数
    app.gotoAd_Splash_Jpg(MainActivity.this,R.drawable.logo_page,1000);//跳转到静态图开屏
```
其中，R.drawable.logo事你传入的logo页图片。因为开屏需要加载素材时间，为了防止空白期，默认需要一张首页logo图片（下同）
### 2、动态开屏广告
```java
    MDFullApp app=new MDFullApp(MainActivity.this);
    //参数1:上下文  参数2:默认显示的logo图  参数3:默认在logo图停留的时间毫秒数
    app.gotoAd_Splash_Gif(MainActivity.this,R.drawable.logo_page,1000);//跳转到动态图开屏
```
### 3、视频开屏广告
```java
    MDFullApp app=new MDFullApp(MainActivity.this);
    //参数1:上下文  参数2:默认显示的logo图  参数3:默认在logo图停留的时间毫秒数
    app.gotoAd_Splash_Video(MainActivity.this,R.drawable.logo_page,1000);//跳转到视频开屏
```
## b、信息流
信息流分为图文信息流和视频信息流，图文信息流分为大图（单图），三图，自定义（可以满足一定约束下自定义广告布局），视频信息流的样式暂时固定。
### 1.信息流大图
导包（给出了sdk的包，系统的包默认AndroidStudio里面使用Alt+Enter导入）
```java
import mf.com.adsdk_demo.framework.MDFullApp;
import mf.com.adsdk_demo.utils.network.NetworkUtil;
```
使用
```java
RelativeLayout container = findViewById(R.id.container);
MDFullApp app = new MDFullApp(MainActivity.this);
//参数：1.上下文  2.信息流类型  3.容器  4.信息流回调
app.insertAd_InformationFlow(MainActivity.this, MDFullApp.TYPE_INFORMATIONFLOW_BIGIMAGE,
        container, new MDFullApp.InformationFlowCallback() {
            @Override
            public void onRequestResult(boolean success, String msg) {//方法内部处于非主线程，请注意
                if (!success) {
                    MainActivity.this.runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            AlertDialog dialog = new AlertDialog.Builder(MainActivity.this).create();
                            dialog.setMessage("没有匹配到广告");
                            dialog.setButton(AlertDialog.BUTTON_POSITIVE, "返回", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialog, int which) {
                                    dialog.dismiss();
                                    finish();
                                }
                            });
                            dialog.setCancelable(false);
                            dialog.show();
                        }
                    });
                }
            }

            @Override
            public void onAttachResult(boolean success, String msg) {

            }
        });
```
其中，insertAd_InformationFlow（Context context,String adType,ViewGroup container,MDFullApp.InformationFlowCallback callback）方法参数如下：参数1:上下文对象，参数2:广告类型，参数3:广告视图的容器，参数4:广告请求回调
广告回调中，public void onRequestResult(boolean success, String msg)返回数据获取结果字符窜，public void onAttachResult(boolean success, String msg)返回广告attach在container上的结果(暂时没有回调)。

### 2.信息流三图
信息流三图和大图类似。将app.insertAd_InformationFlow方法的第二个参数替换为MDFullApp.TYPE_INFORMATIONFLOW_THREEIMAGE即可。

### 3.自定义图文信息流
导包（给出了sdk的包，系统的包默认AndroidStudio里面使用Alt+Enter导入）
```java
import mf.com.adsdk_demo.framework.MDFullApp;
import mf.com.adsdk_demo.utils.network.NetworkUtil;
import mf.com.adsdk_demo.view.customview.CustomViewUtil;
```
使用
```java
final RelativeLayout container=findViewById(R.id.container);
ViewGroup parent = (ViewGroup) LayoutInflater.from(MainActivity.this).inflate(R.layout.layout_custom_ad, null);
container.addView(parent);//请保证广告视图可见
TextView title = parent.findViewById(R.id.title);
TextView desc = parent.findViewById(R.id.desc);
TextView adFlag = parent.findViewById(R.id.adflag);
ImageView img = parent.findViewById(R.id.img);
//自定义广告
//参数：1.上下文  2.信息流回调
new CustomViewUtil().insertCustom(MainActivity.this, new CustomViewUtil.CustomCallback() {
    @Override
    public void onResult(boolean success, String msg) {
        if (!success) {
            MainActivity.this.runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    //移除视图
                    container.removeAllViews();
                    AlertDialog dialog = new AlertDialog.Builder(MainActivity.this).create();
                    dialog.setMessage("没有匹配到广告");
                    dialog.setButton(AlertDialog.BUTTON_POSITIVE, "返回", new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            dialog.dismiss();
                        }
                    });
                    dialog.setCancelable(false);
                    dialog.show();
                }
            });
        }
    }
}, title, desc, img, adFlag, parent);
```
自定义图文信息流需要满足一些条件：1.传入的view参数必须完整，否则无法广告计费，2.广告规定必须有一个广告标识符（这里传入的是TextView类型的adFlag）。3.所有传入的view必须保证可见（也即根视图为decorview），否则无法广告计费。

### 7、视频信息流
视频信息流需要在activity生命周期中控制播放器的回收，下面给出一个例子
```java
import android.app.Activity;
import android.app.AlertDialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.util.Log;
import android.view.ViewGroup;

import mf.com.adsdk_demo.framework.MDFullApp;
import mf.com.adsdk_demo.utils.network.NetworkUtil;
import mf.com.adsdk_demo.view.video.AdVideoViewPlayer;
import mf.com.adsdk_demo.view.video.VideoView;
import mf.com.mdfullad_try_demo.R;

public class MobileInfomationFlowDemoActivity extends Activity{

    ViewGroup container;//广告视图容器
    MDFullApp app;//广告应用对象
    AdVideoViewPlayer player;//广告播放器
    boolean isFirstPlay=true;//是否第一次播放
    boolean canExit=false;//是否可以退出
    boolean isPlayClick=false;//是否点击了播放
    boolean isInitialized=false;//是否初始化完成

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_mobile_infomation_flow_demo);
        container = findViewById(R.id.container);
        app = new MDFullApp(MainActivity.this);
        //创建播放器
        player=new AdVideoViewPlayer(MainActivity.this, NetworkUtil.isWiFiConnected(MainActivity.this),
                ViewGroup.LayoutParams.MATCH_PARENT, new VideoView.OnPlayerStateListener() {
            @Override
            public void onBeforeInitial() {//还没有初始化的时候
                isPlayClick=true;//点击了播放按钮
            }

            @Override
            public void onVideoInitializes() {//初始化结束的时候
                canExit=true;
                isInitialized=true;
            }

            @Override
            public void onVideoStart() {//开始播放的时候

            }

            @Override
            public void onVideoProgressChange(int progress) {//播放进度改变

            }

            @Override
            public void onVideoPause() {//视频暂停

            }

            @Override
            public void onVideoResume() {//视频激活

            }

            @Override
            public void onVideoStop() {//视频结束退出

            }

            @Override
            public void onVideoCompletion() {//视频播放完

            }

            @Override
            public void onVideoError(String msg) {//视频出错

            }

            @Override
            public void onVideoInfo(String msg) {//视频信息输出

            }
        });
        //插入视频广告
        参数：1.上下文  2.容器  3.播放器  4.信息流回调
        app.insertAd_Mobile_InformationFlow(MainActivity.this, container, player,
                new MDFullApp.MobileInformationFlowCallback() {
                    @Override
                    public void onRequestResult(boolean success, String msg) {
                        if(!success){
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    //移除视图
                                    player.deAttach();

                                    AlertDialog dialog = new AlertDialog.Builder(MainActivity.this).create();
                                    dialog.setMessage("没有匹配到广告");
                                    dialog.setButton(AlertDialog.BUTTON_POSITIVE, "返回", new DialogInterface.OnClickListener() {
                                        @Override
                                        public void onClick(DialogInterface dialog, int which) {
                                            dialog.dismiss();
                                            finish();
                                        }
                                    });
                                    dialog.setCancelable(false);
                                    dialog.show();
                                }
                            });
                        }
                    }

                    @Override
                    public void onAttachResult(boolean success, String msg) {

                    }
                });
    }

    @Override
    protected void onResume() {
        super.onResume();
        if(!isFirstPlay) {
            Log.e("onResume","不是第一次播放");
            player.resume();
        }
        isFirstPlay=false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        if(!isFirstPlay) {
            Log.e("onResume","不是第一次播放");
            if(player!=null) {
                player.resume();
            }
        }
        isFirstPlay=false;
    }

    @Override
    protected void onPause() {
        super.onPause();
        if(player!=null) {
            player.close();
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(player!=null) {
            player.close();
        }
    }

    @Override
    public void onBackPressed() {
        if((NetworkUtil.isWiFiConnected(this)&&canExit)
                ||(NetworkUtil.isMobileDataEnable(this)&(!isPlayClick||(isPlayClick&&isInitialized)))) {
            super.onBackPressed();
        }
    }
}
```
其中，视频信息流广告的播放器对象需要和activity保持相同的生命周期，随着activity的退出而退出。但是因为播放器内部线程原因导致播放器无法回收释放问题，所以需要做初始化的状态判断（只有初始化后才可以退出activity）。并且为了防止切换activity回来后视频画面丢失，也需要做视频恢复，代码见上方。

## 四、数据返回
请求广告可能返回204（没有匹配到广告），因为广告生成服务器会决定是否返回广告。因此对于自定义图文广告，应该判断在没有返回广告时，容器的移除或隐藏处理（防止占用空间）

## 五、发布版本建议隐藏sdk内部输出，以免无法通过应用市场审核。
```java
MDFullADConstant.debug=false;//禁止内部日志输出
```

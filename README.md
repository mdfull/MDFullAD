# MDFullAD
米赋广告SDK开发文档

## 一、使用
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
### 2、适配API23以上权限配置
添加动态权限申请
```java
        PermissionUtil.getUtil().requestPermission(this, new String[]{//申请必要的权限
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
            }
        });
```
注册权限回调
```java
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        PermissionUtil.getUtil().onRequestPermissionsResult(110);//注册权限申请回调
    }
```
### 3、基本使用方法
在请求广告前注册app,参数1:存放SpaceId的map，参数2:选择运行环境（测试|线上）
```java
    MDFullApp.register(map,MDFullApp.ENV_RELEASE);//注册
```
其中参数1的所有类型广告id需要开发者向米赋相关人员申请（目前），然后开发者可以register时传入完整包含id的map，也可以在需要时调用MDFullApp.getApp().setSpaceIds(map);进行动态设置。
## 二、广告类型
## a、开平广告
开屏广告由sdk托管，直接intent跳转，可以在主界面还未设置数据时直接跳转。
### 1、静态开屏广告
```java
    Intent intent=new Intent(this,JpgAdSplashActivity.class);
    startActivity(intent);
```
### 2、动态开屏广告
```java
    Intent intent2=new Intent(this,GifAdSplashActivity.class);
    startActivity(intent2);
```
### 3、视频开屏广告
```java
    Intent intent3=new Intent(this,VideoAdSplashActivity.class);
    startActivity(intent3);
```
## b、信息流
信息流分为图文信息流和视频信息流，图文信息流分为大图（单图），三图，自定义（可以满足一定约束下自定义广告布局），视频信息流的样式暂时固定。
```java
import android.app.Activity;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.RelativeLayout;
import android.widget.TextView;

import ad.sdk.mf.com.ad_sdk.R;
import ad.sdk.mf.com.adsdk.framework.MDFullApp;
import ad.sdk.mf.com.adsdk.framework.MDFullManager;
import ad.sdk.mf.com.adsdk.utils.config.Constant;
import ad.sdk.mf.com.adsdk.view.customview.CustomViewUtil;
import ad.sdk.mf.com.adsdk.view.dialog.DialogUtil;

public class InfomationFlowActivity extends Activity implements MDFullApp.AppRecall {

    RelativeLayout container;
    MDFullApp app;
    MDFullManager manager;

    public static final String KEY = "infomation";
    public static final String VALUE_ONE_PICTURE = "one_picture";
    public static final String VALUE_THREE_PICTURE = "three_picture";
    public static final String VALUE_MULTI = "custom";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_infomation_flow);
        
        container = findViewById(R.id.container1);
        app = MDFullApp.getApp();
        app.setAppRecall(this);
        manager = MDFullManager.getManager();
        
        String value = getIntent().getStringExtra(KEY);
        if (value.equals(VALUE_ONE_PICTURE)) {//非自定义，大图
            manager.insertAd_InformationFlow(this, app, container, MDFullManager.MODE_IMG_ONE_IMAGE, null);
        } else if (value.equals(VALUE_THREE_PICTURE)) {//非自定义，三图
            manager.insertAd_InformationFlow(this, app, container, MDFullManager.MODE_IMG_THREE_IMAGE, null);
        } else if (value.equals(VALUE_MULTI)) {//自定义，自定义任意样式，但必须保证有标题，描述，图片，广告样式标签，广告根视图
            System.out.println("进入图文信息流");
            ViewGroup parent = (ViewGroup) LayoutInflater.from(this).inflate(R.layout.layout_custom_ad, null);
            container.addView(parent);//请保证广告视图可见
            TextView title = parent.findViewById(R.id.title);
            TextView desc = parent.findViewById(R.id.desc);
            TextView adFlag = parent.findViewById(R.id.adflag);
            ImageView img = parent.findViewById(R.id.img);
            CustomViewUtil.Callback callback = new CustomViewUtil.Callback() {
                @Override
                public void showSuccess(String opCode) {
                    System.out.println(opCode + ":展示成功");
                }

                @Override
                public void showFail(String opCode, String msg) {
                    System.out.println(opCode + ":展示失败:" + msg);
                }

                @Override
                public void adClick(String opCode) {
                    System.out.println(opCode + ":广告点击");
                }

                @Override
                public void onInfo(String opCode, String msg) {
                    System.out.println(opCode + ":返回信息：" + msg);
                }
            };
            //发起广告
            System.out.println("发起广告");
            new CustomViewUtil(callback)
                    .requestData("custom", Constant.图文信息流广告)//请求数据
                    .applyAll(title, desc, img, adFlag, parent);//将数据应用到视图上
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(DialogUtil.getUtil().isShow()){
            DialogUtil.getUtil().close();
        }
    }

    @Override
    public void onFail(final String msg) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                DialogUtil.getUtil().showSimpleMessageDialog(InfomationFlowActivity.this, msg, "确定",
                        new DialogInterface.OnClickListener() {
                            @Override
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
            }
        });
    }
}
```
图文广告需要activity实现回调 implements MDFullApp.AppRecall，这个回调当广告没有匹配到的时候（服务端会进行匹配判断是否返回广告），会返回204，标示没有匹配到广告，所以当没有匹配到广告时需要在回调中做处理，比如弹出对话框。在activity退出时记得关闭对话框（如果有的话），否则造成窗体泄漏。
上面的例子，根据intent传过来的key判断是何种类型的图文广告，然后调用manager的显示方法，一条指令请求广告。
manager.insertAd_InformationFlow(this, app, container, MDFullManager.MODE_IMG_ONE_IMAGE, null);
参数1时activity的context，参数2是MDFullApp的单例（请确保在此前已经通过MDFullApp.register()方法注册过，否则通过MDFullApp.getApp()获取的将是null），参数3是广告的容器（广告需要插入容器中），参数4是广告类型（目前图文固定可选有两种：大图，三图），参数5是广告请求回调（用于开发时调试错误信息）。
广告请求回调结构如下：
```java
MDFullManager.AdCallback adCallback=new MDFullManager.AdCallback() {
        @Override
        public void onShowFail(String opCode) {
            //广告展示失败
        }

        @Override
        public void onShowSuccess(String opCode) {
            //广告展示成功
        }

        @Override
        public void onAdClick(String opCode) {
            //广告被点击
        }
};
```
自定义图文，使用方式如下：
```java
CustomViewUtil.Callback callback = new CustomViewUtil.Callback() {
    @Override
    public void showSuccess(String opCode) {
        System.out.println(opCode + ":展示成功");
    }

    @Override
    public void showFail(String opCode, String msg) {
        System.out.println(opCode + ":展示失败:" + msg);
    }

    @Override
    public void adClick(String opCode) {
        System.out.println(opCode + ":广告点击");
    }

    @Override
    public void onInfo(String opCode, String msg) {
        System.out.println(opCode + ":返回信息：" + msg);
    }
};
//发起广告
new CustomViewUtil(callback)
        .requestData("custom")//请求数据
        .applyAll(title, desc, img, adFlag, parent);//将数据应用到视图上
```
使用方式分两步：
1、定义自定义图文的回调
2、请求自定义图文广告
其中，requestData只需要传入开发者自己定义的opCode，applyAll需要传入自定义广告view视图中的控件（请确保都传入且不为空）。

### 7、视频信息流
视频信息流需要在activity生命周期中控制播放器的回收
```java
    import android.app.Activity;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.ViewGroup;

import ad.sdk.mf.com.ad_sdk.R;
import ad.sdk.mf.com.adsdk.framework.MDFullApp;
import ad.sdk.mf.com.adsdk.framework.MDFullManager;
import ad.sdk.mf.com.adsdk.view.dialog.DialogUtil;
import ad.sdk.mf.com.adsdk.view.video.VideoViewPlayer;

public class MobileInfomationFlowDemoActivity extends Activity implements MDFullApp.AppRecall {

    ViewGroup container;
    MDFullApp app;
    MDFullManager manager;
    VideoViewPlayer player;
    boolean isFirstPlay=true;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_mobile_infomation_flow_demo);
        container=findViewById(R.id.container);
        app=MDFullApp.getApp();
        app.setAppRecall(this);
        manager=MDFullManager.getManager();
        manager.insertAd_MobileInformationFlow(this, app, container, true, true,
                new VideoViewPlayer.PlayerGetter() {
                    @Override
                    public void onGet(VideoViewPlayer player) {
                        MobileInfomationFlowDemoActivity.this.player=player;//获取播放器实例
                    }
                },null);
    }

    @Override
    protected void onResume() {
        super.onResume();
        System.out.println("激活activity");
        System.out.println("第一次播放:"+isFirstPlay);
        if(player!=null&&!isFirstPlay) {
            System.out.println("player重启");
            player.resume();
        }
    }

    @Override
    protected void onPause() {
        super.onPause();
        if(player!=null) {
            player.close();
        }
        System.out.println("暂停activity");
        isFirstPlay=false;
        System.out.println("第一次播放:"+isFirstPlay);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(player!=null) {
            player.close();
            player=null;
        }
        if(DialogUtil.getUtil().isShow()){
            DialogUtil.getUtil().close();
        }
    }

    @Override
    public void onFail(final String msg) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                DialogUtil.getUtil().showSimpleMessageDialog(MobileInfomationFlowDemoActivity.this, msg, "确定",
                        new DialogInterface.OnClickListener() {
                            @Override
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
            }
        });
    }
}
```
视频信息流需要开发者自行处理视频的销毁，避免内存泄漏。在activity暂停或退出时进行销毁，恢复时进行视频恢复。

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
在请求广告前注册app,参数1:context，参数2:存放SpaceId的map，参数3:选择运行环境（测试|线上）
```java
    MDFullApp.register(context,map,MDFullApp.ENV_RELEASE);//注册
```
请求广告，参数1:操作码（开发者自己定义），参数2:广告类型常量，参数3:ResponseGetter（回调广告数据）
```java
    MDFullApp app;
    app=MDFullApp.getApp();
    app.requestAd(opCode, 广告类型, getter);//请求广告
```
在获取到广告后，进行显示
```java
    MDFullManager manager;
    manager=MDFullManager.getManager();
    manager.insertAd_InformationFlow(context, app, ResponseAd, container, opCode,
                            广告类型, MDFullManager.AdCallback);
```
## 二、广告类型
### 1、静态开屏广告
开屏广告由sdk托管，直接intent跳转
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
### 4、信息流大图
```java
    RelativeLayout container;//广告容器
    MDFullApp app;
    MDFullManager manager;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_infomation_flow);

        container=findViewById(R.id.container);

        app=MDFullApp.getApp();
        manager=MDFullManager.getManager();
        
        app.requestAd("information_One_image", Constant.信息流大图广告, this);
    }
    
    @Override
    public void onGet(MDFullApp.ResponseObject obj) {
        manager.insertAd_InformationFlow(this, app, obj.ad, container1, obj.opCode,
                 MDFullManager.MODE_IMG_ONE_IMAGE, this);
    }
```
### 5、信息流三图
```java
    RelativeLayout container;
    MDFullApp app;
    MDFullManager manager;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_infomation_flow);

        container=findViewById(R.id.container);

        app=MDFullApp.getApp();
        manager=MDFullManager.getManager();
                app.requestAd("information_three_image", Constant.信息流三图广告, this);
    }

    @Override
    public void onGet(MDFullApp.ResponseObject obj) {
        manager.insertAd_InformationFlow(this, app, obj.ad, container1, obj.opCode,
                MDFullManager.MODE_IMG_THREE_IMAGE, this);
    }
```
### 6、信息流图文
惜细流图文提供开发者自定义视图布局，将布局中广告title,desc,img,adflag做参数传入
```java
    RelativeLayout container;
    MDFullApp app;
    MDFullManager manager;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_infomation_flow);

        container=findViewById(R.id.container);

        app=MDFullApp.getApp();
        manager=MDFullManager.getManager();

                ViewGroup parent= (ViewGroup) LayoutInflater.from(this).inflate(R.layout.layout_custom_ad,null);
                container.addView(parent);//请保证广告视图可见
                TextView title=parent.findViewById(R.id.title);
                TextView desc=parent.findViewById(R.id.desc);
                TextView adFlag=parent.findViewById(R.id.adflag);
                ImageView img=parent.findViewById(R.id.img);
                CustomViewUtil.Callback callback=new CustomViewUtil.Callback() {
                    @Override
                    public void showSuccess(String opCode) {
                        System.out.println(opCode+":展示成功");
                    }

                    @Override
                    public void showFail(String opCode,String msg) {
                        System.out.println(opCode+":展示失败:"+msg);
                    }

                    @Override
                    public void adClick(String opCode) {
                        System.out.println(opCode+":广告点击");
                    }

                    @Override
                    public void onInfo(String opCode,String msg) {
                        System.out.println(opCode+":返回信息："+msg);
                    }
                };
                //发起广告
                System.out.println("发起广告");
                new CustomViewUtil(callback)
                        .requestData("custom",Constant.图文信息流广告)//请求数据
                        .putTitle(title)//输入标题控件
                        .putDesc(desc)//输入描述控件
                        .putImage(img)//输入图片控件
                        .putAdFlag(adFlag)//输入广告标示控件
                        .apply(parent);//输入广告父控件
    }
```
### 7、视频信息流
视频信息流需要在activity生命周期中控制播放器的回收
```java
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
        manager=MDFullManager.getManager();

        app.requestAd("mobileinfomationflow",Constant.视频信息流广告,this);
    }

    @Override
    public void onGet(MDFullApp.ResponseObject obj) {
                player=manager.insertAd_MobileInformationFlow(this,app,obj.ad,container,obj.opCode,
                        true,true, this);
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
    }
```

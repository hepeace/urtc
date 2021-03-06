# 学生端Android SDK指南

## 1. 功能说明

  - 支持加入房间
  - 支持房间内发布本地媒体流
  - 支持播放网络音频
  - 支持本地截图
  - 支持本地录制
  - 支持IM自定义消息
  - 支持订阅教师端的MP4流

## 2. 下载资源

  - 可以下载Demo、SDK、API 接口文档  

    [现在下载IM以及URTC](https://github.com/ucloud/urtc-android-demo/tree/dev)   

## 3. 开发语言以及系统要求

  - 开发语言：Java
  - 系统要求：Android 5.0以上版本

##  4. 开发环境

  - android studio 需要android SDK

### 搭建开发环境

  - 下载urtc android SDK包，SDK包为aar格式，名称为`ucloudrtclib`开头加版本号加一串8位识别码，可以参考github上的接入demo。   
  - 将aar 文件拷贝到自己的`lib` 目录下，然后添加到`lib` 中，修改要使用sdk模块目录下`build.gradle`，确保已经添加了如下依赖，如下所示：

```java
    dependencies {
    implementation (name: 'ucloudrtclib_1.6.2_ee9bbf43', ext: 'aar')
    implementation (name: 'ucloudimlib_1.0_e053d8bc', ext: 'aar')
    implementation (name: 'ucloud_base_1.0_6279f2ae', ext: 'aar')
    
```

  - 如果项目混淆，请在混淆中添加一下urtc和uim的 混淆规则。


```java
 //rtc部分
-keep class com.ucloudrtclib.sdkengine.**{*;}
-keep class com.ucloudrtclib.sdkengine.define.*{*;}
-keep enum com.ucloudrtclib.sdkengine.define.*{*;}
-keep class com.ucloudrtclib.common.**{*;}
-keep class com.ucloudrtclib.monitor.**{*;}
-keepclassmembers class com.ucloudrtclib.sdkengine.UCloudRtcSdkEnv {
    public static <methods>;
}

-keepclassmembers interface com.ucloudrtclib.sdkengine.UCloudRtcSdkEngine {
    public <methods>;
    public static <methods>;
}
#保护类名和类内所有成员
-keep class org.webrtc.** {
    *;
}

//im部分

-keep class com.ucloud.business.im.sdkengine.**{*;}
-keep class com.ucloud.business.im.sdkengine.define.*{*;}
-keep enum com.ucloud.business.im.sdkengine.define.*{*;}
-keep class com.ucloud.business.base.UcloudBusSdkEventListener{*;}
-keep class com.ucloud.business.base.UCloudBusVirtualEngine{*;}
-keep class com.ucloud.business.base.UcloudBusSdkEngineImpl{*;}
-keep class com.ucloud.business.im.logicengine.UcloudIMSdkEngineImpl{*;}


-keepclassmembers interface com.ucloud.business.im.sdkengine.UcloudIMSdkEngine {
    public <methods>;
    public static <methods>;
}

-keepclassmembers interface com.ucloud.business.im.sdkengine.UcloudIMSdkEventListener {
    public <methods>;
    public static <methods>;
}

-keepclassmembers interface com.ucloud.business.base.UcloudBusSdkEventListener {
    public <methods>;
    public static <methods>;
}

```

  - 添加权限

在 Android 6.0 (API 23)开始，用户需要在应用运行时授予权限，而不是在应用安装时授予，并分为正常权限和危险权限两种类型。    
在实时音视频SDK 中，用户需要在进入音视频通话房间前动态申请 `CAMERA`、`RECORD\_AUDIO`、`WRITE\_EXTERNAL\_STORAGE`权限，具体可以参考[Android官方文档](https://developer.android.com/training/permissions/requesting?hl=zh-cn)。

```java
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus" />
<uses-feature android:glEsVersion="0x00020000" android:required="true" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.BLUETOOTH" />

```

## 5. 初始化

### 5.1 引擎环境初始化

主要配置`android context sdkmode`以及`AppID` ，测试用的`SEC\_KEY`，日志等级。

```java
public class UCloudRtcApplication extends Application {
        @Override
        public void onCreate() {
            super.onCreate();
            URTCSdkEnv.initEnv(getApplicationContext(), this);
            URTCSdkEnv.setLogLevel(URTCSdkLogLevel.URTC_SDK_LogLevelInfo) ;
            URTCSdkEnv.setSdkMode(URTCSdkMode.RTC_SDK_MODE_TRIVAL);
            URTCSdkEnv.setTokenSeckey(CommonUtils.SEC_KEY);
            WindowManager windowManager = (WindowManager) 
            getSystemService(Context.WINDOW_SERVICE);
            DisplayMetrics outMetrics = new DisplayMetrics();
            windowManager.getDefaultDisplay().getMetrics(outMetrics);
            CommonUtils.mItemWidth = outMetrics.widthPixels / 3;
            CommonUtils.mItemHeight = CommonUtils.mItemWidth;
        }
    }
```
### 5.2 创建rtc和im引擎

```java

sdkEngine = UCloudRtcSdkEngine.createEngine(eventListener);
imEngine = UcloudIMSdkEngine.createEngine(mIMSdkEventListener);

```
### 5.3 继承实现`UCloudRtcSdkEventListener` 实现事件处理


```java
//rtc回调
UCloudRtcSdkEventListener eventListener = new UCloudRtcSdkEventListener() {
        @Override
        public void onServerDisconnect() {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    ToastUtils.shortShow(RoomActivity.this, " 服务器已断开");
                    stopTimeShow();
                    onMediaServerDisconnect() ;
                }
            });
        }
    
        @Override
        public void onJoinRoomResult(int code, String msg, String roomid) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    if (code == 0) {
                        ToastUtils.shortShow(RoomActivity.this, " 加入房间成功");
                        startTimeShow();
                    }else {
                        ToastUtils.shortShow(RoomActivity.this, " 加入房间失败 "+
                                code +" errmsg "+ msg);
                        Intent intent = new Intent(RoomActivity.this, ConnectActivity.class);
                        onMediaServerDisconnect() ;
                        startActivity(intent) ;
                        finish();
                    }
    
                }
            });
        }
            
   更多具体请参考代码以及rtc文档 。
  
  //im 回调
    UcloudIMSdkEventListener mIMSdkEventListener = new UcloudIMSdkEventListener(){

        @Override
        public void onSendCustomerMsg(String msg) {
            Log.d(TAG, "onIMSendCustomerMsg : "+ msg);
            try {
                JSONObject jsonObject = new JSONObject(msg);
                String content  = jsonObject.getString("msg");
            } catch (JSONException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void onReceiveCustomMsg(String msg) {
            Log.d(TAG, "onReceiveCustomMsg: "  + msg);
            JSONObject jsonObject = null;
            try {
                jsonObject = new JSONObject(msg);
                JSONObject data = jsonObject.getJSONObject("msg");
                String content = data.getString("content");
                Log.d(TAG, "onReceiveCustomMsg: "  + " content: "+ content.toString());
//                JSONObject contentJson = new JSONObject(content);
//                Log.d(TAG, "onReceiveCustomMsg: "  + " contentJson: "+ contentJson.getString("title"));
            } catch (JSONException e) {
                e.printStackTrace();
            }

        }

        @Override
        public void onReceiveMsg(String msg) {
            Log.d(TAG, "onReceiveMsg common msg: "+ msg);
        }

        @Override
        public void onJoinRoomResult(int code, String msg, String roomid) {
            Log.d(TAG, "onIMJoinRoomResult: " + code + " msg: "+ msg + " roomid: "+ roomid);
        }

        @Override
        public void onLeaveRoomResult(int code, String msg, String roomid) {
            Log.d(TAG, "onIMLeaveRoomResult: " + code + " msg: "+ msg + " roomid: "+ roomid);
            receiveLeaveIMQuit = true;
            leaveRoom();
        }

        @Override
        public void onRejoiningRoom(String roomid) {
            Log.d(TAG, "onIMRejoiningRoom: "+ roomid);
        }

        @Override
        public void onRejoinRoomResult(String roomid) {
            Log.d(TAG, "onIMRejoinRoomResult: "+ roomid);
        }

        @Override
        public void onServerDisconnect() {
            Log.d(TAG, "onIMonServerDisconnect: ");
        }

        @Override
        public void onServerConnect() {
            Log.d(TAG, "onIMonServerConnect: ");
        }
    };
      
```

### 5.4 获取SDK 引擎 并进行基础配置

```java
sdkEngine.setAudioOnlyMode(true) ; // 设置纯音频模式
sdkEngine.configLocalCameraPublish(false) ; // 设置摄像头是否发布
sdkEngine.configLocalAudioPublish(true) ; // 设置音频是否发布，用于让sdk判断自动发布的媒体类型
sdkEngine.configLocalScreenPublish(false) ; // 设置桌面是否发布，作用同上
sdkEngine.setStreamRole(URTCSdkStreamRole.URTC_SDK_STREAM_ROLE_BOTH);// 流权限
sdkEngine.setAutoPublish(true) ; // 是否自动发布
sdkEngine.setAutoSubscribe(true) ;// 是否自动订阅
sdkEngine.setVideoProfile(UCloudRtcSdkVideoProfile.matchValue(mVideoProfile)) ;// 摄像头输出等级
```

## 6. 建立通话

### 6.1 加入房间



```java
//rtc 加入房间
UCloudRtcSdkAuthInfo info = new UCloudRtcSdkAuthInfo();
        info.setAppId(mAppid);
        info.setToken(mRoomToken);
        info.setRoomId(mRoomid);
        info.setUId(mUserid);
        Log.d(TAG, " roomtoken = " + mRoomToken);
        sdkEngine.joinChannel(info);
 
 //im 加入房间 
 UCloudIMSdkAuthInfo imSdkAuthInfo = new UCloudIMSdkAuthInfo();
        imSdkAuthInfo.setAppId(mAppid);
        imSdkAuthInfo.setRoomId(mRoomid);
        imSdkAuthInfo.setUId(mUserid);
//        imSdkAuthInfo.setIMRole(UCloudIMRole.UCLOUD_IM_ROLE_TEACHER);
        imEngine.joinChannel(imSdkAuthInfo);
```

### 6.2 发布媒体流

  - 如果配置了自动发布无需调用发布视频接口，SDK会在用户成功加入房间后自动发布，只需要监听事件调用渲染接口即可。

```java
sdkEngine.setAutoPublish(mPublishMode == CommonUtils.AUTO_MODE ? true : false);
```

  - 如果配置了手动发布需要调用`sdkEngine`引擎的`publish`接口 配置手动/自动发布。


```java
sdkEngine.publish(UCloudRtcSdkMediaType mtype, boolean hasvideo, boolean hasaudio)
回调事件
public void onLocalPublish(int code, String msg, UCloudRtcSdkStreamInfo info
```


  - 媒体发布类型

现在的类型包括两大类，需要传入`publish`接口的`mtype`,`hasvideo`,`hasaudio`参数各不相同，混合类型是单一类型的组合，具体代码可参阅urtcdemo的`RoomActvity`中的处理。 

 - 混合类型：音频+屏幕捕捉、视频+屏幕捕捉   
 - 单一类型        
   1） 音频（mtype:urtc_sdk_media_type_video,hasvideo:false,hasaudio:true）    
   2） 视频（mtype:urtc_sdk_media_type_video,hasvideo:true,hasaudio:true）    
   3） 屏幕捕捉（mtype:urtc_sdk_media_type_screen,hasvideo:true,hasaudio:false）    



  - 渲染本地媒体流

在`onLocalPublish` 回调成功后，再函数中可以调用视频渲染。

```java
localrenderview.setBackgroundColor(Color.TRANSPARENT);
sdkEngine.startPreview(info.getmMediatype(), localrenderview);
不想渲染时可以调用停止渲染接口
sdkEngine.stopPreview(UCloudRtcSdkMediaType mediatype
```
  - 取消发布媒体流

```java
sdkEngine.unPublish(UCloudRtcSdkMediaType mtype)
回调事件
public void onLocalUnPublish(int code, String msg, UCloudRtcSdkStreamInfo info
```

### 6.3 播放网络音频
等待更新


### 6.4 本地截图

view传本地的就是本地截图，远端的view就是远端的截图。

```java
private void addScreenShotCallBack(UCloudRtcSdkSurfaceVideoView view){
        view.setScreenShotBack(new UcloudRTCSceenShot() {
            @Override
            public void onReceiveRGBAData(ByteBuffer rgbBuffer, int width, int height) {
                final Bitmap bitmap = Bitmap.createBitmap(width * 1, height * 1, Bitmap.Config.ARGB_8888);
                bitmap.copyPixelsFromBuffer(rgbBuffer);
                String name = "/mnt/sdcard/urtcscreen_"+System.currentTimeMillis() +".jpg";
                File file = new File(name);
                try {
                    FileOutputStream out = new FileOutputStream(file);
                    if (bitmap.compress(Bitmap.CompressFormat.JPEG, 100, out)) {
                        out.flush();
                        out.close();
                    }
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                Log.d(TAG, "screen shoot : " + name);
                ToastUtils.shortShow(RoomActivity.this,"screen shoot : " + name);
            }
        });
}
```

### 6.5 本地录制	

等待更新


### 6.6 IM自定义消息	

```java
 mSendMsgBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                try {
                    JSONObject content = new JSONObject();
                    content.put("title","test customer");
                    imEngine.pushCustomerMessage("test",content.toString());
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }
        });
```

### 6.7 订阅媒体流

如果配置了自动订阅无需调用订阅视频接口，SDK会在用户成功加入房间后查看房间已有的可以订阅的流并进行逐一订阅，当有新用户加入房间时也会自动订阅他推的流。   
如果配置了手动订阅需要调用sdkEngine引擎的subscribe接口。 

```java
sdkEngine.setAutoSubscribe(mScribeMode == CommonUtils.AUTO_MODE ? true : false);
```

  - 订阅媒体流

```java
sdkEngine.subscribe(UCloudRtcSdkStreamInfo info)
//回调事件
public void onSubscribeResult(int code, String msg, UCloudRtcSdkStreamInfo info
```

  - 渲染订阅的媒体流

在onSubscribeResult回调成功后，再函数中可以调用视频渲染。

```java
sdkEngine. startRemoteView(UCloudRtcSdkStreamInfo info, UCloudRtcSdkSurfaceVideoView renderview)
//不想渲染时可以调用定制渲染接口
sdkEngine.stopPreview(UCloudRtcSdkMediaType mediatype
```

  - 取消订阅媒体流

```java
sdkEngine. subscribe(UCloudRtcSdkStreamInfo info) 
//回调事件
public void onUnSubscribeResult(int code, String msg, UCloudRtcSdkStreamInfo info)
```

### 6.8 用户发布和订阅的权限控制

权限分为发布，订阅，全部权限，全部权限包括了发布和订阅。

```java
//接口
public int setStreamRole(UCloudRtcSdkStreamRole role)
//调用
sdkEngine.setStreamRole(mRole);
```


### 6.9 离开房间


```java
//rtc离开房间申请
sdkEngine.leaveChannel() ;
//等待rtc离开房间回调 
@Override
        public void onLeaveRoomResult(int code, String msg, String roomid) {
            Log.d(TAG, "onRTCLeaveRoomResult: " + code + " msg: "+ msg + " roomid: "+ roomid);
            receiveRTCQuit = true;
            leaveRoom();
        }
 //im离开房间申请
 imEngine.leaveChannel();
 //IM离开房间回调
  @Override
        public void onLeaveRoomResult(int code, String msg, String roomid) {
            Log.d(TAG, "onIMLeaveRoomResult: " + code + " msg: "+ msg + " roomid: "+ roomid);
            receiveLeaveIMQuit = true;
            leaveRoom();
        }
```

### 6.10 destroy清理
```java
        UCloudRtcSdkEngine.destory();
        UcloudIMSdkEngine.destory();
 ```       

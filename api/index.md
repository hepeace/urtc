# API文档

实时音视频URTC SDK 支持多个客户端，不同客户端的API方法不完全一致。    
这里介绍Web，Windows，Android，iOS/macOS这几个平台的核心功能 API，帮助你快速了解各个平台之间的差异。    

|方法|Web|Windows|Android|iOS|
|-|-|-|-|-|
|初始化|[client](https://github.com/ucloud/urtc-sdk-web#client-constructor)|[Windows方法](https://docs.ucloud.cn/urtc/sdk/VideoStart)|[Android方法](https://docs.ucloud.cn/urtc/sdk/VideoStart)|[iOS方法](https://docs.ucloud.cn/urtc/sdk/VideoStart)|
|加入房间|[joinRoom](https://github.com/ucloud/urtc-sdk-web#client-joinroom)|[joinChannel](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-joinChannel)|joinChannel|joinRoom|
|离开房间|[leaveRoom](https://github.com/ucloud/urtc-sdk-web#client-leaveroom)|[leaveChannel](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-leaveChannel)|leaveChannel|leaveRoom|
|房间类型|[type](https://github.com/ucloud/urtc-sdk-web#client-constructor)|[setChannelType](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-setChannelType)|setClassType|roomType|
|用户权限|[setRole](https://github.com/ucloud/urtc-sdk-web#client-setrole)|[setStreamRole](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-setStreamRole)|setStreamRole|streamProfile|
|发布流|[publish](https://github.com/ucloud/urtc-sdk-web#client-publish)|[publish](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-publish)|publish|publish|
|视频分辨率|[setVideoProfile](https://github.com/ucloud/urtc-sdk-web#client-setvideoprofile)|[setVideoProfile](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-setVideoProfile)|setVideoProfile|videoProfile|
|监听事件|[on](https://github.com/ucloud/urtc-sdk-web#client-on)|[EventListener](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-regRtcEventListener)|EventListener|EngineDelegate|
|订阅流|[subscribe](https://github.com/ucloud/urtc-sdk-web#client-subscribe)|[subscribe](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-subscribe)|subscribe|subscribeMethod|
|销毁实例|**NA**|[destroy](https://github.com/ucloud/urtc-win-demo/tree/master/doc#class-destroy)|destory|destory|

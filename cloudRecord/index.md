# 开通云端录制服务

URTC 云端录制，是URTC针对实时音视频研发的录制服务。  

![](/images/record/cloudRecord-new.png)

无需额外的SDK，通过简单的操作方法，帮助开发者快速、灵活的实现录制服务，实现一对一、一对多的音视频通话或直播的录制。   
客户端上开启、停止录制，参考[云端录制SDK代码示例](urtc/cloudRecord/RecordStart)。

## 功能说明：  

  - 单流录制，支持录制指定某个用户的音频流和视频流  
  - 合流录制，录制房间内，所有用户的音视频，合流录制为一个音视频文件  
  - 支持多种混流风格 
  - 录制支持添加时间水印、文字水印、图片水印
  - 支持存储在Ucloud的对象存储US3的指定存储空间  
  
## 1、开通录制服务
  
 客户端上使用录制功能，首先需要在控制台上开通录制服务。    
 [登录控制台](https://passport.ucloud.cn/?service=https://console.ucloud.cn/#login)，实时音视频 URTC 产品应用下，增值功能中的录制服务内，点击【开通服务】。  
  
  ![ ](/images/record/openRecord.png)
  
## 2、配置录制文件的存储的路径  
  
开通录制服务后，点击【新建对象存储】，新建录制的存储路径。        
  
![ ](/images/record/newBucket.png)
	 
 - US3 中已经配置了对象存储的情况下，可以直接选择配置的存储空间，点击【确定】，配置完毕。  

![ ](/images/record/newBucket2.png) 
	 
 - 配置完毕后，看到存储空间的状态为【正常】。
 
![ ](/images/record/creatBucket44.png)

## 3、新建对象存储空间

 - US3 中没有对象存储的情况下，需要去对象存储US3 产品中，创建存储空间；再按照上述步骤配置存储空间。  

![ ](/images/record/creatBucket.png) 

 >了解更多对象存储的内容，可以  [查看这里](https://docs.ucloud.cn/ufile/quick/quick_start)  。  


### 3.1 创建存储空间

点击【创建存储空间】，选择地域、空间类型、业务组，**输入存储空间域名**，点击【确定】，保存配置。    

![ ](/images/record/creatBucket1.png)

### 3.2 创建令牌

点击【创建令牌】，输入令牌名称。   

![ ](/images/record/lingpai.png)

点击【选择存储空间】，选择已经创建的存储空间。    

![ ](/images/record/lingpai2.png)

选择令牌的有效时长和令牌权限。

![ ](/images/record/lingpai4.png)

点击【确认】后，保存令牌。


	 
  
  

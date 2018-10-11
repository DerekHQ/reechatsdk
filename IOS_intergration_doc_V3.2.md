## IOS app intergration document

Develop Tool Xcode
```xml
SDK description：
一：This SDK support two running modes：
	1. real-time voice
	    Some participants entered one voice room. They can chat each other like with phone call.
	2. offline voice
	    User can record his voice to a audio file with sdk, then sdk will store the file to server side and feedback a record url.
	    User can dispatch the record url to any users and they can playback the record url again.
	    
二：This SDK support video conference and broadcast the conference to internet with rtmp stream or hls stream;

三：caution:
    This SDK can only compile for iphone device(no simulator)
```

#### 一：download sdk and decompress it
######  [download site](http://www.reechat.org/)


---
#### 二：Project Setting
###### 2.1. Config static library：
```xml
1. Inport reechat.framework into xcode project.
2. Ensure you have set -ObjC in the following field. (Build Settings->Other Linker Flags)
important: Please link these framwork or staic libs in Build Phases tab. 
```
![image](https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/ios_link.png)

###### 2.2. Config privileges：
```xml
  Please insert the two key-values into info.plist file,
  Privacy – Microphone Usage Description
  Privacy – Camera Usage Description
```

***
#### 三：intergration：

###### Register sdk callback：

```Objective-C
# Call this function in main thread
# ReeChatMain::sharedInstance().RegisterCallback(sdkCallBack);
 This interface is used to register callback for the whole sdk. You must call it first before use any other interface. 
```


###### Set your own pamramaters if neccesarry
```Objective-C
# Call this function in main thread
 ReeChatMain::sharedInstance().SetSdkParam("RoomServerAddr", "gateway.reechat.org:8080");

 This interface is used to set your own pamramaters when you establish your own reechat server system.  
  RoomServerAddr: set your own gateway server dns or ip address.  
  VoiceUploadUrl: set your own record file upload url.  
  LiveServerAddr: set your live server dns or ip address.
```


###### Initialize SDK:
```Objective-C
# Call this function in main thread
 ReeChatMain::sharedInstance().InitSDK(appid, appkey);
 This interface is used to Initialize SDK
```

Parameters Description：

parameters|type|description
:-:|:-:|:-:
appId|String|appId get from website
key|String|appkey get from website

###### Set user info:
```Objective-C
# Call this function in main thread
  ReeChatMain::sharedInstance().SetUserInfo(username, "");
```

###### Set room info:
```Objective-C
# Call this function in main thread
# You should call this interface before request join room
ReeChatMain::sharedInstance().SetRoomType(kVoiceAndVideo|kMusicLowMark|kVideoLarge_veryHighDefinition|kConferenceNeedMix, 4);
Function prototype：
    void SetRoomType(int media_type, int layout_mode);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
media_type|int|media type
layout_mode|int|the parameter is used to define video layout mode
```xml
media_type can be combine with the following enumeration value：
	enum enMediaType
    {
        kVoiceOnly = 0x01,
        kVideoOnly = 0x02,
        kVoiceAndVideo = 0x03,
    };
    
    enum enMediaProperty
    {
        kVoiceLowMark = 0x00,
        kVoiceMediumMark = 0x20,
        kVoiceHighMark = 0x40,
        kMusicLowMark = 0x60,
        kMusicMediumMark = 0x80,
        kMusicHighMark = 0xA0,
        
        kVideoSmall_normalDefinition = 0x00,
        kVideoSmall_highDefinition = 0x800,
        kVideoSmall_veryHighDefinition = 0x1000,
        kVideoMedium_normalDefinition = 0x1800,
        kVideoMedium_highDefinition = 0x2000,
        kVideoMedium_veryHighDefinition = 0x2800,
        kVideoLarge_normalDefinition = 0x3000,
        kVideoLarge_highDefinition = 0x3800,
        kVideoLarge_veryHighDefinition = 0x4000
    };
    
    enum enConferenceProperty
    {
        kConferenceNormal = 0x00,
        kConferenceNeedForward = 0x100,
        kConferenceNeedMix = 0x200,
    };
```

###### Request join room：
```Objective-C
# Call this function in main thread
  ReeChatMain::sharedInstance().RequestJoinRoom(roomid);
```

###### Open or close mobile speaker：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
  ReeChatMain::sharedInstance().UseLoudSpeaker(enable);
```

###### Adjust pcm volumn：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
  ReeChatMain::sharedInstance().AdjustSpeakerVolume(value);
```
Parameters Description：

parameters|type|description
:-:|:-:|:-:
	volumeValue|float|value can choosed between 0 to 10

###### Request quit room ：
```Objective-C
# Call this function in main thread
	ReeChatMain::sharedInstance().requestLeavePlatformRoom();
```

###### start record voice into a audio file
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
  ReeChatMain::sharedInstance().startRecordVoice(true,true,1);
Function ptototype：
  int StartIMRecord(boolean needConvertWord,boolean isPersis, float scaleLevel);
```
Parameters Description：

parameters|type|description
:-:|:-:|:-:
needConvertWord|boolean|whether translate voice to word
isPersis|boolean|wheher Persistence the record file
scaleLevel|float|alter the scale factor value，default value is 1.0



###### stop record voice into a audio file
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
  ReeChatMain::sharedInstance().StopIMRecord();
```


###### Cancel record voice into a audio file：
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
  ReeChatMain::sharedInstance().CancelIMRecord();
```
###### Start Play the record url
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
  ReeChatMain::sharedInstance().StartPlayIMVoice(voice_url);
Function prototype：
  int StartPlayIMVoice(const char* voiceUrl);
```
Parameters Description：

parameters|type|description
:-:|:-:|:-:
filePath|String|the record url from anther user


###### Stop Play the record url
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
  ReeChatMain::sharedInstance().StopPlayIMVoice();
Function prototype：
	int StopPlayIMVoice();
```

---

### 四：Interface about video conference

###### Create a video render view to use：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
# You can create multi views for video render, but you have the responsibility to destroy them.
    ReeChatMain::sharedInstance().CreateAVideoWindow();
```

###### Destroy a video render view：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
# You can create multi views for video render, but you have the responsibility to destroy them.
    ReeChatMain::sharedInstance().DestroyAVideoWindow();
```

###### Start show local video of mobile camera：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
	ReeChatMain::sharedInstance().ObserverLocalVideoWindow(true, window_ptr);
Function prototype：
  SdkErrorCode ObserverLocalVideoWindow(bool enable, void* ptrWindow = nullptr);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
enable|boolean|true:open remote video, false:close remote video
ptrWindow|void*|The render view created by CreateAVideoWindow interface

###### Stop show local video of mobile camera：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
	ReeChatMain::sharedInstance().ObserverLocalVideoWindow(false);
Function prototype：
  SdkErrorCode ObserverLocalVideoWindow(bool enable, void* ptrWindow = nullptr);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
enable|boolean|true:open remote video, false:close remote video
ptrWindow|void*|The render view created by CreateAVideoWindow interface

###### Start send local video：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
	ReeChatMain::sharedInstance().StartSendVideo();
Function prototype：
	int StartSendVideo();
```

###### Stop send local video：
```Objective-C
# Call this function in main thread
# The interface can only be called after joined room
	ReeChatMain::sharedInstance().StopSendVideo();
Function prototype：
	int StopSendVideo();
```

###### Observe Remote conference video：
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
	ReeChatMain::sharedInstance().ObserverRemoteTargetVideoV1("", ovideoView);
Function prototype：
	int ObserverRemoteTargetVideoV1(const char* userID, void* ptrWindow);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
userID|char*|design the specified user video stream, "" mean confenrence video stream
ptrWindow|void*|The render view created by CreateAVideoWindow interface

###### Stop observe Remote conference video：
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
	ReeChatMain::sharedInstance().ObserverRemoteTargetVideoV1("", null);
Function prototype：
	int ObserverRemoteTargetVideoV1(const char* userID, void* ptrWindow);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
userID|char*|design the specified user video stream, "" mean confenrence video stream
ptrWindow|void*|nullptr will notify server to stop send video stream 

###### Switch sending stram source：
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
	ReeChatMain::sharedInstance().SwitchVideoSource(kVideoSourceBackCamera);
Function prototype：
	int SwitchVideoSource(enVideoSource sourceIndex, void* ptrScreenView = nullptr);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
sourceIndex|int| 1:front camera, 2:back camera, 3:screen share window, 4:video stream of local file, like mp4, mkv
ptrScreenView|void*|user can specify the window being captured when sourceIndex is 3


###### Enable beauty Filter：
```Objective-C
# Call this function in main thread
# Can only be called when being outside the conference room
	ReeChatMain::sharedInstance().EnableBeautifyFilter(sender.isOn);
Function prototype：
	void EnableBeautifyFilter(bool enabled);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
enabled|boolean|true:use beauty filter；false:unuse beauty filter

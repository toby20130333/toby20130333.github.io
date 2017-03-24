---
layout:     post
title:      "基于Qt封装声网平台直播API简析"
subtitle:   " \"基于Qt框架的直播API简析\""
date:       2017-03-24
author:     "Tobyyi"
header-img: "http://qtclub.qiniudn.com/acb8f1b840a28e0157f27d66a5f54122.jpg"
tags:
    - Qt
    - C++
    - Agora
    - 直播
---

> “Yeah It's on. ”

## 声网SDK开发需要C++基础

 * 掌握必要的C++基础是学习和使用第三方SDK的二次开发的重要基石

## 声网SDK能做什么

 * 声网Agora.io提供一个极简SDK让开发者接入SD-RTN™实时虚拟通信网，在任何App和网站实现高质量的音频通话、视频通话、全互动直播。。

 * 语音通话  采用独家音频编解码 Codec，全球首个使用全频带音质超宽频，将普通电话音质提高 6 倍，将网络通话音质提高 2 倍。超宽频音质，码率更低，听觉体验更好

 * 视频通话  前苹果视频算法团队核心成员主力开发，独家高清视频 Codec，可在 720P 和 360P 间自由切换。与网络深度结合，基于人眼视觉体验质量优化。

 * 游戏语音 端到端平均延时 76ms，MOBA Gank 更默契 稳定高可用，聊一晚上毫无压力   网络不好也能聊

 * 全互动直播  全球首个基于 UDP 的直播 SDK


## 声网SDK API说明

* [API说明](https://docs.agora.io/cn/user_guide/live_broadcast/windows_live.html)


* Windows API解析

    * setChannelProfile 该方法用于设置频道模式(Profile)。Agora RtcEngine 需知道应用程序的使用场景(例如通信模式或直播模式),从而使用不同的优化手段

    * enableVideo 该方法用于开启视频模式。可以在加入频道前或者通话中调用，在加入频道前调用，则自动开启视频模式，在通话中调用则由音频模式切换为视频模式。关闭视频模式使用disablevideo方法。

    * setupLocalVideo 该方法设置本地视频的显示相关属性。
    应用程序通过调用此接口绑定本地视频流的显示视窗(view)，并设置视频显示模式。在应用程序开发中，通常在初始化后调用该方法进行本地视频设置，然后再加入频道。退出频道后，绑定仍然有效，如果需要解除绑定，可以指定空(NULL)View调用setupLocalVideo。

    * setVideoProfile 每个Profile对应一套视频参数，如分辨率、帧率、码率等。当设备的摄像头不支持指定的分辨率时，SDK会自动选择一个合适的摄像头分辨率，但是编码分辨率仍然用setVideoProfile 指定的。

    * setClientRole  在加入频道前，用户需要通过本方法设置观众(默认)或主播模式。在加入频道后，用户可以通过本方法切换用户模式。

    * enableDualStreamMode  该方法设置单流（默认）或者双流直播模式，仅在直播模式有效。


    * setRemoteVideoStreamType 该方法指定接收远端用户的视频流大小。使用该方法可以根据视频窗口的大小动态调整对应视频流的大小，以节约带宽和计算资源。
    本方法调用状态将在下文的onApiCallExecuted中返回。Agora SDK默认收到视频小流，节省带宽。如需使用视频大流，调用本方法进行切换

    * joinChannel 该方法让用户加入通话频道，在同一个频道内的用户可以互相通话，多个用户加入同一个频道，可以群聊。使用不同App ID的应用程序是不能互通的。如果已在通话中，用户必须调用leaveChannel退出当前通话，才能进入下一个频道

    * leaveChannel 离开频道，即挂断或退出通话。joinChannel后，必须调用leaveChannel以结束通话，否则不能进行下一次通话。不管当前是否在通话中，都可以调用leaveChannel，没有副作用。如果成功，则返回值为0。leaveChannel会把会话相关的所有资源释放掉

    ......

## 基于Qt的C++类直播接口实现


  * 继承IRtcEngineEventHandler的直播接口类 实现直播接口回调处理

  * 继承QObject 实现信号槽的连接


```
#include "SDK/include/IAgoraRtcEngine.h"

#include <QObject>

using namespace agora::rtc;
//#define USE_WINDOWS_API

class QCAGEngineEventHandler : public QObject,public IRtcEngineEventHandler
{
    Q_OBJECT
public:
    QCAGEngineEventHandler(QObject *parent = NULL);
    ~QCAGEngineEventHandler();

    void setMsgReceiver(QObject *hWnd = NULL);
    QObject * getMsgReceiver() {return m_hMainWnd;}

    virtual void onJoinChannelSuccess(const char* channel, uid_t uid, int elapsed);
    virtual void onRejoinChannelSuccess(const char* channel, uid_t uid, int elapsed);
    virtual void onWarning(int warn, const char* msg);
    virtual void onError(int err, const char* msg);
    virtual void onAudioQuality(uid_t uid, int quality, unsigned short delay, unsigned short lost);
    virtual void onAudioVolumeIndication(const AudioVolumeInfo* speakers, unsigned int speakerNumber, int totalVolume);
    
    virtual void onLeaveChannel(const RtcStats& stat);
    virtual void onRtcStats(const RtcStats& stat);
    virtual void onMediaEngineEvent(int evt);

    virtual void onAudioDeviceStateChanged(const char* deviceId, int deviceType, int deviceState);
    virtual void onVideoDeviceStateChanged(const char* deviceId, int deviceType, int deviceState);

    virtual void onLastmileQuality(int quality);
    virtual void onFirstLocalVideoFrame(int width, int height, int elapsed);
    virtual void onFirstRemoteVideoDecoded(uid_t uid, int width, int height, int elapsed);
    virtual void onFirstRemoteVideoFrame(uid_t uid, int width, int height, int elapsed);
    virtual void onUserJoined(uid_t uid, int elapsed);
    virtual void onUserOffline(uid_t uid, USER_OFFLINE_REASON_TYPE reason);
    virtual void onUserMuteAudio(uid_t uid, bool muted);
    virtual void onUserMuteVideo(uid_t uid, bool muted);
    virtual void onApiCallExecuted(const char* api, int error);

    virtual void onLocalVideoStats(const LocalVideoStats& stats);
    virtual void onRemoteVideoStats(const RemoteVideoStats& stats);
    virtual void onCameraReady();
    virtual void onVideoStopped();
    virtual void onConnectionLost();
    virtual void onConnectionInterrupted();

    virtual void onUserEnableVideo(uid_t uid, bool enabled);

    virtual void onStartRecordingService(int error);
    virtual void onStopRecordingService(int error);
    virtual void onRefreshRecordingServiceStatus(int status);
private:
    QObject *       m_hMainWnd;
};


```

## Qt-直播SDK的使用 待续...

    * 基于QApplication的app

    * QtWidgets类中使用

```
    待续......

```

## 注意事项

    * 开发者需要在[声网官网](http://www.agora.io/cn/)进行注册 获取APPID


## 应用场景

* 方便Qt开发者关注业务逻辑实现
* 实现直播跨平台客户端开发

## 有问题反馈

在使用中有任何问题，欢迎反馈给我，可以用以下联系方式跟我交流

* 邮件(373955953#qq.com, 把#换成@)
* QQ: 39559539234
* QQ群:312125701
* github: [@寒山-居士](https://github.com/toby20130333)

## 关于作者

```javascript

  var duoduozhijiao = {
    nickName  : "寒山-居士",
    site : "http://www.heilqt.com",
    blog : "http://blog.heilqt.com"
  }

```
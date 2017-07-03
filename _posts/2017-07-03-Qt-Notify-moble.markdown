---
layout:     post
title:      "Qt移动推送功能"
subtitle:   " \"Base Notify for Qt Mobile\""
date:       2017-07-03
author:     "Tobyyi"
header-img: "https://yunba.io/static/img/home.png"
catalog:    true
tags:
    - QtQuick
    - Qt
    - Notify
---

>  移动消息推送
> <br/>
>  加油吧 骚年

# Qt移动推送
说到移动应用，大家都觉得移动嘛，当然是Java和Object-c来做啦，什么推送啊，各种系统调用啊，其实不然？如果你了解Qt，
你就知道我说的不然，也有所道理。

说道几点**移动推送**

一.**目前Android的移动的消息、通知推送**

1）**轮询(Pull)方式**

应用程序应当阶段性的与服务器进行连接并查询是否有新的消息到达，你必须自己实现与服务器之间的通信，例如消息排队等。而且你还要考虑轮询的频率，如果太慢可能导致某些消息的延迟，如果太快，则会大量消耗网络带宽和电池。

2）**SMS(Push)方式**

在Android平台上，你可以通过拦截SMS消息并且解析消息内容来了解服务器的意图，并获取其显示内容进行处理。这是一个不错的想法，我就见过采用这个方案的应用程序。这个方案的好处是，可以实现完全的实时操作。但是问题是这个方案的成本相对比较高，我们需要向移动公司缴纳相应的费用。我们目前很难找到免费的短消息发送网关来实现这种方案。

3）**持久连接(Push)方式**

     这个方案可以解决由轮询带来的性能问题，但是还是会消耗手机的电池。IOS平台的推送服务之所以工作的很好，是因为每一台手机仅仅保持一个与服务器之间的连接，事实上C2DM也是这么工作的。不过刚才也讲了，这个方案存在着很多的不足之处，就是我们很难在手机上实现一个可靠的服务，目前也无法与IOS平台的推送功能相比。
4)  **Android Cloud to Device Messaging (C2DM)**

     是一个用来帮助开发者从服务器向Android应用程序发送数据的服务。该服务提供了一个简单的、轻量级的机制，允许服务器可以通知移动应用程序直接与服务器进行通信，以便于从服务器获取应用程序更新和用户数据。C2DM服务负责处理诸如消息排队等事务并向运行于目标设备上的应用程序分发这些消息。关于C2DM具体使用过程，大家可以去查阅相关的资料.
5)  **使用第三方推送服务**

      第三方平台有商用的也有免费的，我们可以根据实现情况使用。关于国内的第三方平台，我感觉目前比较不错的就是极光推送。关于极光推送目前是免费的，我们可以直接使用。关于详细情况，大家可以查看它的主页：http://www.jpush.cn/index.jsp，这里不再详细描述。
　　关于MQTT的方案，国内最近出现基于 mqtt 的第三方解决方案 云巴 (http://yunba.io)，据了解是原 极光推送 CTO 创办的，有兴趣的朋友可以研究下。
　　关于国外的第三方平台我也见过几个：http://www.push-notification.org/。有兴趣的朋友可以查阅相关信息。使用第三方平台就需要使用别人的服务器，关于这点，你懂的。

 6） **自己搭建推送服务器**
   这不是一件轻松的工作，当然可以根据各自的需要采取合适的方案。
   基于以上几点推送方案，再结合自己应用的实际情况，我选择了第三方推送服务，***理由有以下几点***：
   1.第三方推送服务，包含各个移动平台，对于开发者来说，可以做到推送通知到各种平台，节省成本
   2.数据可以达到同步，而不需要做任何多余的事情
   3.节省开发时间。

  二.**ios和mac平台的推送**

  1.Apple Push Notification Service，苹果服务器自己的推送服务，已经集成到ios系统当中。
  APNS提供了两项基本的服务：消息推送和反馈服务。
  消息推送：使用流式TCP套接字将推送通知作为二进制数据发送给APNs。消息推送有分别针对开发和测试用的sandbox、发布产品的两个接口，每个都有各自的地址和端口。不管用哪个接口，都需要通过TLS或SSL，使用SSL证书来建立一个安全的信道。提供者编制通知信息，然后通过这个信道将其发送给APNs。
注：sandbox:   gateway.sandbox.push.apple.com:219
产品接口：gateway.push.apple.com:2195
反馈服务：可以得到针对某个程序的发送失败记录。提供者应该使用反馈服务周期性检查哪些设备一直收不到通知，不需要重复发送通知到这些设备，降低推送服务器的负担。
注：sandbox：feedback.push.apple.com:2196
产品接口：feedback.sandbox.push.apple.com:2196
APNS原理如下：
  首先是应用程序注册消息推送。
  IOS跟APNS Server要deviceToken。应用程序接受deviceToken。
  应用程序将deviceToken发送给PUSH服务端程序(Provider)。
  服务端程序向APNS服务发送消息。
  APNS服务将消息发送给iPhone应用程序。
  通俗描述：
  苹果推送顺序：客户端获取到设备的token，每台设备唯一，传到了app方的服务器，app方服务器将这些devicetoken收集入库，然后需要推送就连接苹果服务器，将客户端的token传过去，当然还得传ios 推送证书，然后交付给苹果服务器，由苹果服务器自己推送给客户端。
  基于APNS服务分析：
  第一阶段：推送服务器(provider)把要发送的消息、目的iPhone的标识打包，发给APNS；
  第二阶段：APNS在自身的已注册Push服务的iPhone列表中，查找有相应标识的iPhone，并把消息发到iPhone；
  第三阶段：iPhone把发来的消息传递给相应的应用程序，并且按照设定弹出Push通知。
  难点：
  1.需要自己写服务器，将收集到的token一个个的发送到APNS服务器，交由APNS来处理
  2.推送证书的购买与设置，苹果开发者需要每年花费99美元来支付开发证书和推送证书等服务
  基于以上考虑，若是自想省去麻烦，可以交付第三方推送来帮你省去服务端的麻烦，当然你也可以自己来处理后端的事情，  我是打算来自己处理，后端采用nodejs+apns协议来处理
  ----------------------以上说推送方案与原理-------------------------------------------------------
  现在来说下如何应用于Qt App当中呢**？**
  1.对于全平台来说，第三方推送无疑是最佳方案，节省开发时间
  2.结合各个移动平台来处理各平台的系统api，另外集成各个系统的第三方SDK，Android使用Java的jni调用，iOS使用Object-c混编，windowphone暂未研究，看官自行补充。
  Qt平台各个系统的第三方SDK加入
  Android：
  pro文件加入：

```
android{
LIBS += -lgnustl_shared
QT += quick androidextras

ANDROID_PACKAGE_SOURCE_DIR = $$PWD/android

SOURCES += \$\$PWD/yunba/notificationclient.cpp \
    $$PWD/yunba/simpleCustomEvent.cpp
    
OTHER_FILES += \$\$PWD/android/AndroidManifest.xml \
    \$\$PWD/android/src/org/qtproject/dduijiao/PushTestWithJava.java\
    \$\$PWD/android/src/org/qtproject/dduijiao/ExtendsQtNative.java \
    \$\$PWD/android/libs/android-support-v4.jar \
    \$\$PWD/android/libs/yunba-sdk-release1.4.5.jar \
    \$\$PWD/android/src/org/qtproject/dduijiao/DemoReceiver.java
HEADERS += \
    \$\$PWD/yunba/notificationclient.h\
    \$\$PWD/yunba/simpleCustomEvent.h
}

iOS平台

    QMAKE_LFLAGS    += -framework OpenGLES
    QMAKE_LFLAGS    += -framework GLKit
    QMAKE_LFLAGS    += -framework CFNetwork
    QMAKE_LFLAGS    += -framework QuartzCore
    QMAKE_LFLAGS    += -framework CoreVideo
    QMAKE_LFLAGS    += -framework CoreAudio
    QMAKE_LFLAGS    += -framework CoreImage
    QMAKE_LFLAGS    += -framework CoreMedia
    QMAKE_LFLAGS    += -framework AVFoundation
    QMAKE_LFLAGS    += -framework AudioToolbox
    QMAKE_LFLAGS    += -framework CoreGraphics
    QMAKE_LFLAGS    += -framework UIKit
    QMAKE_LFLAGS    += -framework Security
    QMAKE_LFLAGS    += -framework SystemConfiguration
ios: LIBS += -L\$\$PWD/yunba/ -lYunBa
INCLUDEPATH += \$\$PWD/yunba
DEPENDPATH += \$\$PWD/yunba
ios: PRE_TARGETDEPS += \$\$PWD/yunba/libYunBa.a

```

以上平台都是加入了第三方推送SDK，关于如何使用与平台相关的做法，参考foruok的博客
推荐几个参考资料 

 [Nodejs下的APNS服务](https://github.com/toby20130333/node-apn/blob/master/examples/sending-to-multiple-devices.js)
 [云巴推送](http://yunba.io/)



**憧憬**

* 实现国内针对QtQuick和Qt最新信息的更新和传播
* 扩大QtQuick和Qt在移动开发领域的知名度
* 兼容各个主流平台的开发
* 为自身的App打下基石


**贡献**

* [寒山-居士](https://github.com/toby20130333)
* [toby520](http://www.heilqt.com)

**注意事项**

* 目前支持Qt5.x以上版本
* 有任何Qt相关的问题可以到[QtQuick博客](http://www.heilqt.com)进行提问或者加入网站底部的QQ群
* 例子中涉及到图片资源，请自行提供(涉及到图片版权问题)
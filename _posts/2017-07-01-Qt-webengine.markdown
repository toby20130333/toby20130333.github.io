---
layout:     post
title:      "分享QML下的WebView如何与JS进行交互"
subtitle:   " \"Brigde C++ And javascript\""
date:       2017-07-01
author:     "Tobyyi"
header-img: "http://qtddui.b0.upaiyun.com/gitdir/shoot3.jpg"
catalog:    true
tags:
    - QtQuick
    - Qt
    - js
---

>  QtQuick基础知识
>  加油吧 骚年

## 分享的起因

* QQ群看到有人问如何在qml中与js进行交互，我们看代码吧。

* 基于qml1代码

```
import QtQuick 1.0
import MuseScore 1.0
import QtWebKit 1.0
import FileIO 1.0 //C++插件
MuseScore {
    menuPath: "Plugins.ABC Web"
    onRun: {}
  FileIO {
        id: myFile
        source: tempPath() + "/my_file.xml"
        onError: console.log(msg)
    }
   WebView {
        javaScriptWindowObjects: QtObject {
            WebView.windowObjectName: "qml"
             function qmlCall(abc) {
                var doc = new XMLHttpRequest()
                var url = "http://abc2musicxml.appspot.com/abcrenderer?abc=" + encodeURIComponent(abc);
                doc.onreadystatechange = function() {
                    if (doc.readyState == XMLHttpRequest.DONE) {
                        var a = doc.responseText;
                        myFile.write(a);
                        readScore(myFile.source);
                        Qt.quit();
                    }
                }
                doc.open("GET", url);
                doc.send()
            }
        }
        preferredWidth: 490
        preferredHeight: 400
        settings.javascriptEnabled:true
        html: "<html><head></head><body>Enter your ABC below <br/><div><textarea id='content' name='content' rows='15' cols='60'></textarea></div><div><a href="http://ddzhj.com/topic/55653ad90fe39eaf0e67c5bd" onClick=\"window.qml.qmlCall(document.getElementById('content').value);\">Convert</a></div></body></html>"
    }
}
```

  * qml2的代码

  ```
  import QtQuick 2.0
import QtWebKit 3.0
import QtWebKit.experimental 1.0
Rectangle {
    width: 1024
    height: 768
    WebView{
        url: "http://localhost"
        anchors.fill: parent
        experimental.preferences.navigatorQtObjectEnabled: true
        experimental.onMessageReceived: {
            console.debug("get msg from javascript")
            experimental.postMessage("HELLO")
        }
    } // webview
} // rectanlge@

  ```

* Html部分

```
<html>
        <body>
        <h1>It just works!</h1>
        <p>Play with me...</p>
        <button onclick="nativecall();">test</button>
        <div id="out"></div>
        <s r c p i t t p y e="t x e t / j a v s a c i r p t ">
        function nativecall(){
            console.log("will try native call");
            var elem = document.getElementById("out"). inner H t M l ="clicked";
            navigator.qt.postMessage('this is a js call');
        }
function jsCall(message){
    var elem = document.getElementById("out"). inner H t M l ="and the other way around " + message;
}
navigator.qt.onmessage = function(ev) {
    jsCall(ev.data);
}
</ script >
</body>
</html>

```

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

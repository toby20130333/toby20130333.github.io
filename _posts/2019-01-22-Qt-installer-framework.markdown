---
layout:     post
title:      "Qt-installer-framework说明大全系列"
subtitle:   " \"A Qt-installer-framework For Qt Application\""
date:       2019-01-22
author:     "Tobyyi"
header-img: "http://qtddui.b0.upaiyun.com/gitdir/bg2.png"
catalog:    true
tags:
    - Deploy
    - C++
    - QtQuick
---

## Qt安装程序框架概述

 Qt安装程序框架提供了一组工具和实用程序，可以一次性创建安装程序，并在所有可支持的桌面Qt平台上部署它们，而无需重写源代码。安装程序将在运行它们的平台上具有原生外观:Linux、Microsoft Windows和OS X。

Qt安装程序框架工具使用一组页面生成安装程序，这些页面在安装、更新或卸载过程中指导用户。您提供可安装的内容，并指定有关它的信息，如产品名称和安装程序以及许可协议的文本。

您可以通过向预定义的页面添加小部件来定制安装程序，或者通过添加整个页面来为用户提供额外的选项。您可以创建向安装程序添加操作的脚本。

###  选择安装类型

* 根据您的使用场景和习惯，您可以为最终用户提供离线或在线安装程序，或者两者兼而有之。

* 这两个安装程序都安装了一个维护工具，稍后可以使用该工具添加、更新和删除组件。离线安装程序包含所有可安装组件，在安装过程中不需要网络连接。在线安装程序只安装维护工具，然后从web服务器上的在线存储库下载和安装组件。因此，在线安装程序二进制文件的大小比离线安装程序二进制文件更小，下载时间也更短。如果终端用户没有安装所有可用的组件，那么下载和运行在线安装程序所花费的总时间也可能比下载和运行离线安装程序所花费的时间要短。

* 最终用户可以使用维护工具在初始安装之后从服务器安装额外的组件，以及在服务器上发布更新时接收到内容的自动更新。但是，只有当您在离线安装程序配置中指定存储库地址，或者最终用户在维护工具设置中自己指定存储库地址时，这才适用于离线安装。

* 创建离线安装程序，使用户能够直接在媒体上下载安装包，以便稍后在计算机上安装。例如，您还可以将安装包分发到CD-ROM或u盘上。

* 创建一个在线安装程序，使用户能够始终安装内容二进制文件的最新版本。


### 推广更新

* 使得在线存储库可用，以便向安装您的产品的最终用户推广更新。提供更新的最简单方法是重新创建存储库并将其上传到web服务器。对于大型存储库，只能更新更改的组件。


### 为安装程序提供内容

* 您可以允许其他内容提供程序将组件作为附加组件添加到安装程序中。组件提供者必须设置包含可安装组件的存储库，并将指向存储库的URL交付给最终用户。最终用户必须在安装程序中配置URL。在包管理器中可以看到附加组件。


## 入门指南

 Qt安装程序框架是作为Qt项目的一部分开发的。框架本身使用Qt，主要是静态Qt发布,无需多于的依赖库,但是，它可以用于安装所有类型的应用程序，包括(但不限于)用Qt构建的应用程序。


### 支持发布平台

* 您可以使用Qt安装程序框架为桌面Qt支持的所有平台创建安装程序。
安装程序已在以下平台进行测试:
1. 微软XP,Win7及以后版本
2. Ubuntu Linux 11.10和更高版本
3. OS X 10.7及以上版本


### 编译Qt-installer-framework步骤

 下面的步骤描述了如何自己构建Qt安装程序框架。如果您已经下载了框架的预构建版本，则可以跳过此步骤。(建议自己动手静态编译Qt安装框架)

* 支持的编译器
  
   Qt安装程序框架可以使用Microsoft Visual Studio 2013及更新版本、GCC 4.7及更新版本、Clang 3.1及更新版本进行编译。

* 配置Qt框架
  
  如果使用静态构建的Qt构建Qt安装程序框架，则不必依赖Qt库，这使您可以将安装程序作为一个文件分发。
  最低要求Qt版本是5.5。
  如果您没有静态编译的Qt版本 则需要以下几种情况进行静态编译Qt源码
  都是最小化静态编译Qt源码 方便构建静态的Qt installer framework的框架

  1. 配置Qt for Windows (最小化静态编译Qt源码)
   ```
    configure -prefix %CD%\qtbase -release -static -static-runtime -target xp -accessibility -no-opengl -no-icu -no-sql-sqlite -no-qml-debug -nomake examples -nomake tests -skip qtactiveqt -skip qtenginio -skip qtlocation -skip qtmultimedia -skip qtserialport -skip qtquick1 -skip qtquickcontrols -skip qtscript -skip qtsensors -skip qtwebkit -skip qtwebsockets -skip qtxmlpatterns -skip qt3d

   ```
  2. 配置Qt for Linux 
   ```
   configure -prefix $PWD/qtbase -release -static -accessibility -qt-zlib -qt-libpng -qt-libjpeg -qt-xcb -qt-pcre -qt-freetype -no-glib -no-cups -no-sql-sqlite -no-qml-debug -no-opengl -no-egl -no-xinput -no-xinput2 -no-sm -no-icu -nomake examples -nomake tests -skip qtactiveqt -skip qtenginio -skip qtlocation -skip qtmultimedia -skip qtserialport -skip qtquick1 -skip qtquickcontrols -skip qtscript -skip qtsensors -skip qtwebkit -skip qtwebsockets -skip qtxmlpatterns -skip qt3d
 
   ```
  3. 配置Qt for Macos
   ```
    configure -prefix $PWD/qtbase -release -static -accessibility -qt-zlib -qt-libpng -qt-libjpeg -no-cups -no-sql-sqlite -no-qml-debug -nomake examples -nomake tests -skip qtactiveqt -skip qtenginio -skip qtlocation -skip qtmultimedia -skip qtserialport -skip qtquick1 -skip qtquickcontrols -skip qtscript -skip qtsensors -skip qtwebkit -skip qtwebsockets -skip qtxmlpatterns -skip qt3d
 
   ```

* 编译Qt installer Framework框架

 1.  从http://code.qt.io/cgit/install-framework/install-framework克隆Qt安装程序框架源代码。获取工具的源代码。
 2. 通过从静态Qt运行“qmake”，然后运行“make”或“nmake”来构建工具。
 3. 注意:要向Qt安装程序框架提供补丁，请遵循标准Qt流程和指导原则。有关更多信息，请参见对Qt的贡献。

* 待续 （继续关注Qt installer framework）
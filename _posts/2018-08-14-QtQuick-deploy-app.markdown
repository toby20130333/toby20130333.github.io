---
layout:     post
title:      "QtQuick程序发布和安装包大全"
subtitle:   " \"A Deploy between QML and C++\""
date:       2018-08-14
author:     "Tobyyi"
header-img: "http://qtddui.b0.upaiyun.com/gitdir/bg2.png"
catalog:    true
tags:
    - Deploy
    - C++
    - QtQuick
---

##  QtQuick程序发布和安装包大全

###  程序开发容易，发布难啊

   很多人对于发布Qt程序表示很不清楚，其实多看看Qt帮助文档就好了，里面说的很清楚，对于各个平台如何发布 都做了英文
   的详细解说
         
### 再此我先说下发布程序包：

  2.1  分三种平台说下
   Windows平台
   微软的Windows平台分为好多版本，就目前比较流行的有XP，Win 7 Win8不是很流行，也在发布之列
   有多少种发布办法。(我们只说动态发布)
   1.使用qt系统自带 windeploy.exe,可以查找和发布该应用程序依赖的qt的dll[注意该办法只能找出大部分依赖的库和插件
   其中只能按照下面方法3进行补充]
   1.1 具体可使用cmd查看下该命令的帮助参数 而qml程序记得加上-qmldir参数  
帮助说明：				
					            
  		     Options:
		  -?, -h, --help            Displays this help.
		  -v, --version             Displays version information.
		  --dir <directory>         Use directory instead of binary directory.
		  --libdir <path>           Copy libraries to path.
		  --plugindir <path>        Copy plugins to path.
		  --debug                   Assume debug binaries.
		  --release                 Assume release binaries.
		  --pdb                     Deploy .pdb files (MSVC).
		  --force                   Force updating files.
		  --dry-run                 Simulation mode. Behave normally, but do not
		                            copy/update any files.
		  --no-patchqt              Do not patch the Qt5Core library.
		  --no-plugins              Skip plugin deployment.
		  --no-libraries            Skip library deployment.
		  --qmldir <directory>      Scan for QML-imports starting from directory.
		  --no-quick-import         Skip deployment of Qt Quick imports.
		  --no-translations         Skip deployment of translations.
		  --no-system-d3d-compiler  Skip deployment of the system D3D compiler.
		  --compiler-runtime        Deploy compiler runtime (Desktop only).
		  --no-compiler-runtime     Do not deploy compiler runtime (Desktop only).
		  --webkit2                 Deployment of WebKit2 (web process).
		  --no-webkit2              Skip deployment of WebKit2.
		  --json                    Print to stdout in JSON format.
		  --angle                   Force deployment of ANGLE.
		  --no-angle                Disable deployment of ANGLE.
		  --no-opengl-sw            Do not deploy the software rasterizer library.
		  --list <option>           Print only the names of the files copied.
		                            Available options:
		                             source:   absolute path of the source files
		                             target:   absolute path of the target files
		                             relative: paths of the target files, relative
		                                       to the target directory
		                             mapping:  outputs the source and the relative
		                                       target, suitable for use within an
		                                       Appx mapping file
		  --verbose <level>         Verbose level.
			

 2.将自家应用程序放置到Qtcreator开发工具安装包目录下面的bin目录下面，试试是否可以运行，删除一些无法的动态库
 
 3.在发布程序之前，可以先release模式编译时候在pro里面加入：
 
 config += console

 这样脱离开发环境运行的程序就可以通过黑色调试窗口看到哪些库或者插件未加载，从而进行库和插件的补充

 4.这里最有问题的可能就是qml应用和第三方dll的使用还有一些插件问题

 5.靠自己的经验，缺啥加啥库(尤其是粒子效果这些依赖很隐秘)
 
 
Mac OSX平台

 1.苹果的Mac OSx平台也分为好多版本，大部分都是10.6以上

2.Mac平台发布可以使用工具macdeploy这个工具，这个工具好像是Qt自带的，可以使用macdeploy -help查看下帮助文档 

```
		   Usage: macdeployqt app-bundle [options]   Options:
		   -verbose=<0-3>     : 0 = no output, 1 = error/warning (default), 2 = normal, 3 = debug
		   -no-plugins        : Skip plugin deployment
		   -dmg               : Create a .dmg disk image
		   -no-strip          : Don't run 'strip' on the binaries
		   -use-debug-libs    : Deploy with debug versions of frameworks and plugins (implies -no-strip)
		   -executable=<path> : Let the given executable use the deployed frameworks too
		   -qmldir=<path>     : Deploy imports used by .qml files in the given path
		   -always-overwrite  : Copy files enven if the target file exists
		macdeployqt takes an application bundle as input and makes it
		self-contained by copying in the Qt frameworks and plugins that
		the application uses.
		
		Plugins related to a framework are copied in with the
		framework. The accessibilty, image formats, and text codec
		plugins are always copied, unless "-no-plugins" is specified.
		
		See the "Deploying an Application on Qt/Mac" topic in the
		documentation for more information about deployment on Mac OS X.

```
使用它基本可以解决无第三方库的项目，查阅项目发布mac osx信息 可以参考Qt帮助文档 Deploying an Application on Mac OS X这一章节
     
 三、类UNIX系统(linux)
     
  linux系统 多种多样，主要还是参考Qt帮助文档 Deploying an Application on X11 Platforms 
  本人很少涉及linux系统，故只能参考Qt帮助文档解说，不过里面有个很重要的脚本还是值得参考   

			#!/bin/sh
			appname=`basename $0 | sed s,\.sh$,,`
			
			dirname=`dirname $0`
			tmp="${dirname#?}"
			
			if [ "${dirname%$tmp}" != "/" ]; then
			dirname=$PWD/$dirname
			fi
			LD_LIBRARY_PATH=$dirname
			export LD_LIBRARY_PATH
			$dirname/$appname "$@"
			

 最后贴上一张基于Windows平台的vs2015-Qt5.10.1发布目录截图

![xxx](http://qtddui.b0.upaiyun.com/gitdir/dp.png)	

这个是我基于C++与QML混编的表情自带发送功能的项目，里面用到了好多qml组件，仅供参考，说的比较简洁，但是各位可以参考Qt帮助文档结合实际项目需要得到实际安装包

### 说下程序安装包的制作

  一、Windows平台

     罗列以下制作方案

    1.Inno Steup制作，该工具是delphi语言开发的一个脚本制作工具

    2.NSIS制作工具，用得少

    3.advanced install，不是很好用

    4.Installshield制作(该工具是收费软件，不过功能也很强大，主要制作一些大型的与数据库等有依赖关系的安装包，小安装包也是可以的，破解的功能受限)

    5.Qt自带的 Qt Installer framework

   二、Mac OSX平台

    目前只是要了一种方案

    1.DMG Canvas工具，这个也是一个收费软件，话说mac下面收费软件就是好用，但是破解的也还行，屌丝只能使用破解版本，支持自定义背景图片

  三、Linux平台

	   参考  QT程序制作deb包并安装在应用程序菜单
	   
 四、以上供参考，有问题可以及时联系，多多指教[寒山-居士](https://github.com/toby20130333)
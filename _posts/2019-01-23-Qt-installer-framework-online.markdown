---
layout:     post
title:      "QIFW说明大全系列-在线安装"
subtitle:   " \"A QIFW For Online Installer\""
date:       2019-01-24
author:     "Tobyyi"
header-img: "http://qtddui.b0.upaiyun.com/gitdir/blog/upyun/bg.png"
catalog:    true
tags:
    - Deploy
    - Installer
    - Qt
---

## Qt 在线安装器制作流程 

* 除了二进制文件中存储的存储库描述之外，在线安装程序还获取存储库描述(update.xml)。创建一个存储库并将其上传到web服务器.然后在配置文件中指定存储库的位置,也就是用于创建安装程序的config.xml文件.



### 创建存储库

* 使用repogen.exe工具创建一个包目录中所有包的在线存储库:
repogen.exe -p <package_directory> <repository_directory>
例如，创建一个只包含org.qt-project.sdk.qt和org.qt-project.sdk.qtcreator，输入以下命令:

	```
	repogen.exe -p packages -i org.qt-project.sdk.qt,org.qt-project.sdk.qtcreator repository
	
	```
	
	这个执行后会在package_directory同目录生成一个repository目录 里面有上面2个包的具体内容和配置信息 另外还有一个Update.xml文件
	
	创建存储库后，将其上传到web服务器。您必须在安装程序配置文件config.xml中指定存储库的位置
	
### 配置存储库信息

* 安装程序配置文件(config.xml)中的< remoterepository >元素可以包含几个存储库的列表。每一个都可以有以下设置:
	<Url>，它指向可用组件的列表。
	<启用>，0禁用此存储库。
	<Username>，在受保护的存储库中用作用户。
	<Password>，它设置在受保护存储库上使用的密码。
	<DisplayName>，它可选地设置要显示的字符串而不是URL。
	URL需要指向列出可用组件的Update.xml文件。例如：
	
	```
	 <RemoteRepositories>
       <Repository>
               <Url>http://www.example.com/packages</Url>
               <Enabled>1</Enabled>
               <Username>user</Username>
               <Password>password</Password>
               <DisplayName>Example repository</DisplayName>
       </Repository>
  </RemoteRepositories>
	```
* 安装程序只有在能够访问存储库时才能工作。如果在安装之后访问存储库，则维护工具将拒绝安装。然而，卸载仍然是可能的。默认情况下可以启用或禁用存储库。对于需要身份验证的存储库，也可以在这里设置详细信息，不过在这里输入密码通常是不可取的，因为密码是以纯文本形式保存的。这里没有设置的身份验证细节将在运行时使用对话框获取。用户可以在运行时绕过这些设置。	
	
	
### 创建安装程序的二进制文件	

* 要使用binarycreator.exe工具创建在线安装程序，输入以下命令:

```
 <location-of-ifw>\binarycreator.exe -t <location-of-ifw>\installerbase.exe -p <package_directory> -c <config_directory>\<config_file> -e <packages> <installer_name>

```
  例如输入以下命令创建一个名为SDKInstaller的安装程序二进制文件,exe不包含org.qt-project.sdk.qt和org.qt-project.qtcreator，因为这些包是从远程存储库下载的:
  
  ```
  binarycreator.exe -p installer-packages -c installer-config\config.xml -e org.qt-project.sdk.qt,org.qt-project.qtcreator SDKInstaller.exe
 
  ```
  
### 减少安装包大小

* 即使组件是从web服务器获取的，binarycreator默认情况下也会将它们添加到安装程序二进制文件中。但是，当安装程序检查web服务器是否有更新时，如果没有新版本，终端用户就不必下载。
或者，您可以创建不包含任何数据并从web服务器获取所有数据的在线安装程序。使用binarycreator工具的-n参数，只将根组件添加到安装程序中。通常根组件是空的，因此只添加根的XML描述。


### 图解说明

1. 安装包目录结构
![installer](http://qtddui.b0.upaiyun.com/gitdir/blog/upyun/online.png)

2. 更新包的目录结构
![updater](http://qtddui.b0.upaiyun.com/gitdir/blog/upyun/updater.png)
## 有问题反馈

在使用中有任何问题，欢迎反馈给我，可以用以下联系方式跟我交流

* 邮件(373955953@qq.com)
* QQ: 373955953
* QQ群:312125701[多多指教Qt/QML社区]
* github: [@寒山-居士](https://github.com/toby20130333)

## 关于作者

```
  var duoduozhijiao = {
    nickName  : "寒山-居士",
    site : "http://www.heilqt.com",
    blog : "http://blog.heilqt.com"
  }

```


* 待续 （继续关注Qt installer framework）
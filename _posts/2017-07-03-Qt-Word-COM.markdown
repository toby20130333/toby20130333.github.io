---
layout:     post
title:      "QAxObject word文档添加页码(页眉和页脚)"
subtitle:   " \"Base QAxObject for Doc Files\""
date:       2017-07-06
author:     "Tobyyi"
header-img: "http://qtddui.b0.upaiyun.com/gitdir/wordto.jpg"
catalog:    true
tags:
    - QtQuick
    - Qt
    - Doc
---

>  基于QAxObject 操作word文档

> <br/>

>  加油吧 骚年

## 分享的起因

* 最近工作需求有QAxObject 操作word文档添加页码的问题，借助[朋友的分享](http://blog.csdn.net/u013704776/article/details/74451462)。

## 解释

* 首先我们看看ActiveQt的非常重要的一个接口

```
QString QAxBase::generateDocumentation()
//Returns a rich text string with documentation for the wrapped COM object. 
//Dump the string to an HTML-file, or use it in e.g. a QTextBrowser widget.

```
  **generateDocumentation**可以打印出来不同的接口的相应的属性，槽函数，以及信号函数。在generateDocumentation打印出来的html文件，不像QtAssistant有很详细的说明，所以我们可以同时借助[VBAWD10.CHM](http://download.csdn.net/detail/luming_1979/4078943),下载该文件后记得右键属性解除锁定，不然无法查看详细的接口说明
  
* 下面就是**pagenumber**的一些相关信息，虽然不是C++语言，但是语言都是相同的大概的意思应该可以看明白。根据下列的提示信息可以有相应的步骤去设置**pagenumbers**.

* 请查看详细示例说明

```cpp

QString filepath = QFileDialog::getOpenFileName(this, tr("Save orbit"), ".", tr("Microsoft Office 2007 (*.docx)"));//获取保存路径  
if (!filepath.isEmpty())
{
QVariantList params;
QAxWidget *excel = new QAxWidget(this);
excel->setControl("Word.Application");//连接Word控件  
QAxObject *document = excel->querySubObject("Documents");
connect(excel, SIGNAL(exception(int, QString, QString, QString)), this, SLOT(someSlot(int, QString, QString, QString)));

//开始获取该axobject对象的帮助文档 以下类似
QString text = document->generateDocumentation();
QFile file("test.html");
if (!file.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput(&file);
txtOutput << text << endl;
file.close();

excel->dynamicCall("SetVisible (bool Visible)", "true");//true显示office窗体，false不显示
excel->setProperty("DisplayAlerts", true);//不显示任何警告信息。如果为true那么在关闭是会出现类似“文件已修改，是否保存”的提示  
QAxObject *presentation = document->querySubObject("Open(const QString&)", filepath);

QString text2 = presentation->generateDocumentation();
QFile file2("test2.html");
if (!file2.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput2(&file2);
txtOutput2 << text2 << endl;
file2.close();

QString name = "Sections";
QAxObject *presentation1 = presentation->querySubObject(name.toStdString().c_str());
QString text3 = presentation1->generateDocumentation();
QFile file3(QString("test_%1.html").arg(name));
if (!file3.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput3(&file3);
txtOutput3 << text3 << endl;
file3.close();

QAxObject *presentation2 = presentation1->querySubObject("First");
QString text4 = presentation2->generateDocumentation();
QFile file4("test4.html");
if (!file4.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput4(&file4);
txtOutput4 << text4 << endl;
file4.close();

QAxObject *presentation3 = presentation2->querySubObject("Headers");//添加页眉
//QAxObject *presentation3 = presentation2->querySubObject("Footers");//添加页脚
QString text5 = presentation3->generateDocumentation();
QFile file5("test5.html");
if (!file5.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput5(&file5);
txtOutput5 << text5 << endl;
file5.close();

params.clear();
params << 1;
QAxObject *presentation4 = presentation3->querySubObject("Item(WdHeaderFooterIndex)", params);
QString text6 = presentation4->generateDocumentation();
QFile file6("test6.html");
if (!file6.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput6(&file6);
txtOutput6 << text6 << endl;
file6.close();

QAxObject *presentation5 = presentation4->querySubObject("PageNumbers");
QString text7 = presentation5->generateDocumentation();
QFile file7("test7.html");
if (!file7.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput7(&file7);
txtOutput7 << text7 << endl;
file7.close();

params.clear();
params << 2;//表示页眉页脚的位置
params << 1;
QAxObject *presentation6 = presentation5->querySubObject("Add(QVariant&, QVariant&)", params);
QString text8= presentation6->generateDocumentation();
QFile file8("test8.html");
if (!file8.open(QIODevice::WriteOnly | QIODevice::Text))
{
//cout << "Open failed." << endl;
//return -1;
}
QTextStream txtOutput8(&file8);
txtOutput8 << text8 << endl;
file8.close();
QString tmpName = docPath;
    tmpName = tmpName.replace(".docx","");
    tmpName = tmpName.replace(".doc","");
    tmpName = tmpName.replace(".DOC","");
    tmpName = tmpName.replace(".DOCX","");
//另存为pdf
    params.clear();
    params<<(tmpName);
    params<<17;//表示pdf
QVariant resVar = presentation->dynamicCall("SaveAs(QVariant&, QVariant&)", params);

presentation->dynamicCall("SaveAs(const QString&)", filepath);
delete presentation;
presentation = NULL;
excel->dynamicCall("Quit()");
delete excel;
excel = NULL;

```

## 总结

* 只是QAxObject  操作office的冰山一角的冰山一角，但是如果要做这方面的话，就用**generateDocumentation**和**VBAWD10.CHM**，绝对的好帮手

**憧憬**

* 实现国内针对QtQuick和Qt最新信息的更新和传播
* 扩大QtQuick和Qt在移动开发领域的知名度
* 兼容各个主流平台的开发
* 为自身的App打下基石


**贡献**

* [朵朵](http://blog.csdn.net/u013704776/article/details/74451462)
* [寒山-居士](https://github.com/toby20130333)
* [博客](http://www.heilqt.com)

**注意事项**

* 目前支持Qt5.x以上版本
* 有任何Qt相关的问题可以到[QtQuick博客](http://www.heilqt.com)进行提问或者加入网站底部的QQ群-312125701
* 例子中涉及到图片资源，请自行提供(涉及到图片版权问题)
---
layout:     post
title:      "Android 资源站点及AS设置Proxy"
subtitle:   ""
date:       2018-08-29
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 工具
    - Android 
---

## AndroidDevTools  

AndroidDevTools 是国内资源集合镜像站点   
该站点主要是收集整理Android开发所需的Android SDK、开发中用到的工具、Android开发教程、Android设计规范，免费的设计素材等。  
[http://www.androiddevtools.cn/][android-proxyweb]


## Android SDK proxy

1 . 启动 Android SDK Manager ，打开主界面，依次选择「Tools」、「Options...」，弹出『Android SDK Manager - Settings』窗口；

2 . 在『Android SDK Manager - Settings』窗口中，在「HTTP Proxy Server」和「HTTP Proxy Port」输入框内填入  
- 东软：mirrors.neusoft.edu.cn 端口：80  
- 郑州大学：mirrors.zzu.edu.cn 端口：80  
并且选中`「Force https://... sources to be fetched using http://...」`复选框。设置完成后单击「Close」按钮关闭`『Android SDK Manager - Settings』`窗口返回到主界面

3 . 然后依次选择「Packages」、「Reload」。即可使用代理更新了。


## Android SDK 更新以及需要安装的package

根据官方文档的描述
- SDK Tools 必须
- SDK Platform-tools 必须
- SDK Platform必须至少安装一个版本
- System Image建议安装
- Android Support建议安装
- SDK Samples建议安装

![android-sdk][android-sdk]


## 参考资料

[Android SDk Manager里面到底哪些东西是必须下载的？][android-sdk-package]

[android-proxyweb]:http://www.androiddevtools.cn/
[android-sdk]:/img/in-post/android-sdk.png
[android-sdk-package]:https://blog.csdn.net/kuangshow0227/article/details/73195037?locationNum=12&fps=1


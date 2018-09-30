---
layout:     post
title:      "Android 开发第一步之SDK"
subtitle:   "Android SDk Manager里面到底哪些东西是必须下载的？"
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

为了考虑兼容性，一般我们只需要下载最新的版本的即可，Android是向下兼容的。

- [ ] tools 目录里的是编译相关的，你要用到哪个版本就下哪个； 
- [ ] 然后 Android M、Android 5.1.1 这些是SDK，主要是提供你开发时候要使用的那个版本api。你要用到哪个版本，就把 SDK Platform下载下来就可以了。比如你开发中如果用到了5.0的api，那么你就要安装5.0的sdk。 
- [ ] Documentation for SDK、Samples for SDK 以及 Source for SDK，别说你看不懂英文。。想学习就下。 
- [ ] 各种 System Images 就算了，都是模拟器用的，直接真机调试最快最方便。 
- [ ] 然后 Google APIs 是调用 Google Service 用的，鉴于国内的网络环境，估计你也用不到。 
- [ ] 再下来 Extras 目录里的好东西也不少。 
- [ ] Support Library 和 Support Repository 需要。 
- [ ] android support repository主要是方便在gradle中使用android support libraries，因为Google并没有把这些库发布到maven center或者jcenter去，而是使用了Google自己的maven仓库。 support library就是提供suppport库给你用的，比如support v4,support v7。 
- [ ] google repository主要是给gradle使用的，方面添加比如Google Play Service的引用。这样gradle就可以使用google的maven仓库中的库了，而不需要去maven centee或者jcenter了。 
- [ ] 下面 Google 开头的一坨东西都是 Google 提供的接口，国内用不上。除了 USB Driver，这个是驱动，Windows 上不装就不能调试。Mac 和 Linux上这一项应该直接是灰的（不可用）。 
- [ ] 最底下一个 Accelerator 是模拟器加速器，真机调试一样可以不用。


看了这张图片以后，就对需要的SDK的包要下载哪些就一目了然了。
![android-sdk][android-sdk]


## 参考资料

[Android SDk Manager里面到底哪些东西是必须下载的？][android-sdk-package]

[android-proxyweb]:http://www.androiddevtools.cn/
[android-sdk]:/img/in-post/android-sdk.png
[android-sdk-package]:https://blog.csdn.net/kuangshow0227/article/details/73195037


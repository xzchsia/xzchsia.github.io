---
layout:     post
title:      "Android Studio之Git版本控制，到底哪些文件不要提交"
subtitle:   ""
date:       2018-11-01 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Android
---

Android Studio开发程序，编译生成后，文件数比较多，占用空间也比较大，进行版本控制的时候，不是所有的文件都需要进行版本控制，每次自动生成的文件，可以去掉。

## 以下就是不需要经过git版本控制来提交的，具体代码可以查看工程中的.gitignore文件:


**IntelliJ IDEA(IDE相关的设置)**

1. .idea
2. *.iml
3. *.ipr
4. *.iws


**Gradle（gradle相关的）**

1. .gradle
2. gradlew.bat
3. build

**Local configuration file (sdk path, etc)(本地的配置文件：sdk的路径等)**

1. local.properties
2. reports
3. /captures
4. jacoco.exec

**Mac system files（mac系统下的文件）**

.DS_Store


**Build application files（构建的app文件）**

1. *.apk
2. *.ap_


**Log Files（log文件）**

*.log

**Android Studio Navigation editor temp files（AS导航编辑临时文件）**

.navigation/

**files for the dex VM（dex包文件）**

*.dex

**Java class files（java编译的class字节码文件）**

*.class

**generated files（工程自动生成的文件）**

1. bin/
2. gen/
3. out
4. lib

**Eclipse project files（使用eclipse工程的一些文件）**

1. .classpath
2. .project
3. .settings/
4. eclipsebin
5. .metadata/

**Proguard folder generated by Eclipse（使用eclipse工程生成的Proguard混淆文件夹）**

proguard/

**NDK（NDK相关的）**

1. obj/
2. jniLibs



![git-ignore-png][git-ignore-png]

是在系统默认的.gitignore文件基础上额外添加的，如果不加 .idea的话会存在不同开发人员开发机上.idea/下文件不同，导致需要提交的问题。而且经过我和团队小伙伴之间的实践.idea目录下的东西AS都会自动生成，并不需要提交到仓库中。



## 参考  
1. [Android开发Git版本控制，到底哪些文件不要提交][android-git-1]
2. [https://github.com/JerryloveEmily/GitIgnoreProject][android-git-2]
3. [https://github.com/github/gitignore/blob/master/Android.gitignore][android-git-3]



[git-ignore-png]:/img/in-post/git-ignore.png
[android-git-1]:https://blog.csdn.net/abren32/article/details/50291535
[android-git-2]:https://github.com/JerryloveEmily/GitIgnoreProject
[android-git-3]:https://github.com/github/gitignore/blob/master/Android.gitignore
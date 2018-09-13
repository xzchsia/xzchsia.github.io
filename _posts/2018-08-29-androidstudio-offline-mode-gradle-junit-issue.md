---
layout:     post
title:      "Android studio离线模式Gradle提示junit的问题"
subtitle:   ""
date:       2018-08-29 17:00:00
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Android 
---
 

### 问题：

Android Studio用gradle编译是版本3.3，在离线（Office Mode）的状态下新建一个项目时出现以下错误：

```
Error: Failed to resolve: junit:junit:4.12  
Failed to resolve: javax.inject:javax.inject:1  
Failed to resolve: javax.annotation:javax.annotation-api:1.2  
Failed to resolve: com.google.code.findbugs:jsr305:2.0.1  
Failed to resolve: org.hamcrest:hamcrest-library:1.3  
Failed to resolve: org.hamcrest:hamcrest-integration:1.3  
Failed to resolve: com.squareup:javawriter:2.1.1  
```

### app中build.gradle文件自动生成的内容：

```
apply plugin: 'com.android.application'  

android {  
	compileSdkVersion 24  
	buildToolsVersion "24.0.3"  
	defaultConfig {  
		applicationId "test.app.stannah.com.uk"  
		minSdkVersion 15  
		targetSdkVersion 24  
		versionCode 1  
		versionName "1.0"  
		testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"  
}  
	buildTypes {  
		release {  
			minifyEnabled false  
			proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'  
		}  
	}  
}  

dependencies {  
	compile fileTree(dir: 'libs', include: ['*.jar'])  
	androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {  
		exclude group: 'com.android.support', module: 'support-annotations'  
	})  
	compile 'com.android.support:appcompat-v7:24.2.1'  
	testCompile 'junit:junit:4.12'  
}  
```

### 原因及解决方法：

**原因很简单，单纯的网络连接问题。解决办法就是将build.gradle中不需要的library去掉，将以下代码展示的库依赖去掉，重新编译即可。**  

```
dependencies {} 内部的  

	compile fileTree(dir: 'libs', include: ['*.jar'])  
androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {  
	exclude group: 'com.android.support', module: 'support-annotations'  
})  
testCompile 'junit:junit:4.12'  

以及 android {} 内部的  

testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"  
```

### 在 stackoverflow中找到相应的解答：

[stackoverflow中类似的解决方法][stackoverflow中类似的解决方法]

遇到相关类似的问题，多Google，多Baidu，一定会找到类似的解决办法。



[stackoverflow中类似的解决方法]: http://stackoverflow.com/questions/40396765/android-studio-v2-2-2-error27-17-failed-to-resolve-junitjunit4-12 


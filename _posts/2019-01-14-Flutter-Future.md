---
layout:     post
title:      "Flutter异步执行之FutureBuilder"
subtitle:   ""
date:       2019-01-14 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Flutter  

---

## 前言   

最近在学习Flutter，看了很多的App Demo，阅读代码中的一些技术特性记录下来，加深自己的理解学习。  

## Flutter异步  

Flutter将异步执行也进行了控件化处理，即：Future。在一些相对耗时的操作中可以使用异步操作来提升用户体验，异步比如IO操作的HTTP请求。除了通过async和await关键字以外，还可以用一个更方便的控件，可生成代码在异步执行时间轴上的快照，并按照自己的需求触发需要的事件，这就是FutureBuilder控件。


## FutureBuilder用法和实现  

先简单讲讲FutureBuilder。
```dart

//FutureBuilder控件
new FutureBuilder<String>(
  future: _calculation, // 用户定义的需要异步执行的代码，类型为Future<String>或者null的变量或函数
  builder: (BuildContext context, AsyncSnapshot<String> snapshot) {      //snapshot就是_calculation在时间轴上执行过程的状态快照
    switch (snapshot.connectionState) {
      case ConnectionState.none: return new Text('Press button to start');    //如果_calculation未执行则提示：请点击开始
      case ConnectionState.waiting: return new Text('Awaiting result...');  //如果_calculation正在执行则提示：加载中
      default:    //如果_calculation执行完毕
        if (snapshot.hasError)    //若_calculation执行出现异常
          return new Text('Error: ${snapshot.error}');
        else    //若_calculation执行正常完成
          return new Text('Result: ${snapshot.data}');
    }
  },
)

```  

FutureBuilder通过子属性future获取用户需要异步处理的代码，用builder回调函数暴露出异步执行过程中的快照。我们通过builder的参数snapshot暴露的快照属性，定义好对应状态下的处理代码，即可实现异步执行时的交互逻辑。
看起来似乎有点晕菜，可能还是不知道怎么用，让我们看看下面这段实战代码：

![futurebuilder][futurebuilder]  

* snapshot.connectionState就是异步函数get(widget.newsType)的执行状态，用户通过定义在ConnectionState.none和ConnectionState.waiting状态下，输出一个居中(Center)显示并且内置文字(Text)为_loading..._的卡片(Card)，其意义即：当异步函数get(widget.newsType)未执行和正在执行时，屏幕正中央显示文字：loading...。  


* 当get(widget.newsType)执行完毕后，snapshot.connectionState的值即变为ConnectionState.done，此时即可输出根据HTTP请求获取到的数据生成对应的ListItem。由于ConnectionState.done是除了ConnectionState.none和ConnectionState.waiting以外的唯一值，所以代码中在switch下用default即可。


由于通过FutureBuilder内的builder()函数即可操控控件的状态和重绘，我们不必通过自己写异步状态的判断和多次使用setState()实现页面上加载中和加载完成显示效果的切换，因为FutureBuilder内部自带了执行setState()的方法。




[futurebuilder]:/img/in-post/flutter-futurebuilder/fultter-futurebuilder.jpg  

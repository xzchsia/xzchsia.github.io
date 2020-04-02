---
layout:     post
title:      "TCP短连接和长连接的区别"
subtitle:   ""
date:       2020-04-02 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Linux
    - TCP

---  


## TCP短连接  

我们模拟一下TCP短连接的情况，client向server发起连接请求，server接到请求，然后双方建立连接。client向server发送消息，server回应client，然后一次读写就完成了，这时候双方任何一个都可以发起close操作，不过一般都是client先发起close操作。为什么呢，一般的server不会回复完client后立即关闭连接的，当然不排除有特殊的情况。从上面的描述看，短连接一般只会在client/server间传递一次读写操作


**短连接的优点是：管理起来比较简单，存在的连接都是有用的连接，不需要额外的控制手段**  



![TCP_SHORT_CONN][TCP_SHORT_CONN]  


## TCP长连接  

接下来我们再模拟一下长连接的情况，client向server发起连接，server接受client连接，双方建立连接。Client与server完成一次读写之后，它们之间的连接并不会主动关闭，后续的读写操作会继续使用这个连接。  

首先说一下TCP/IP详解上讲到的TCP保活功能，保活功能主要为服务器应用提供，服务器应用希望知道客户主机是否崩溃，从而可以代表客户使用资源。如果客户已经消失，使得服务器上保留一个半开放的连接，而服务器又在等待来自客户端的数据，则服务器将应远等待客户端的数据，保活功能就是试图在服务器端检测到这种半开放的连接。  

![TCP_LONG_CONN][TCP_LONG_CONN]    

如果一个给定的连接在两小时内没有任何的动作，则服务器就向客户发一个探测报文段，客户主机必须处于以下4个状态之一：  

* 客户主机依然正常运行，并从服务器可达。客户的TCP响应正常，而服务器也知道对方是正常的，服务器在两小时后将保活定时器复位。  


* 客户主机已经崩溃，并且关闭或者正在重新启动。在任何一种情况下，客户的TCP都没有响应。服务端将不能收到对探测的响应，并在75秒后超时。服务器总共发送10个这样的探测 ，每个间隔75秒。如果服务器没有收到一个响应，它就认为客户主机已经关闭并终止连接。  


* 客户主机崩溃并已经重新启动。服务器将收到一个对其保活探测的响应，这个响应是一个复位，使得服务器终止这个连接。  


* 客户机正常运行，但是服务器不可达，这种情况与2类似，TCP能发现的就是没有收到探查的响应。  


从上面可以看出，TCP保活功能主要为探测长连接的存活状况，不过这里存在一个问题，存活功能的探测周期太长，还有就是它只是探测TCP连接的存活，属于比较斯文的做法，遇到恶意的连接时，保活功能就不够使了。  


![TCP_STATUS][TCP_STATUS]   


在长连接的应用场景下，client端一般不会主动关闭它们之间的连接，Client与server之间的连接如果一直不关闭的话，会存在一个问题，随着客户端连接越来越多，server早晚有扛不住的时候，这时候server端需要采取一些策略，如关闭一些长时间没有读写事件发生的连接，这样可以避免一些恶意连接导致server端服务受损；如果条件再允许就可以以客户端机器为颗粒度，限制每个客户端的最大长连接数，这样可以完全避免某个蛋疼的客户端连累后端服务。  

长连接和短连接的产生在于client和server采取的关闭策略，具体的应用场景采用具体的策略，没有十全十美的选择，只有合适的选择。  



TCP的三次握手和四次挥手  

![tcp-handshake-fin][tcp-handshake-fin]  

## 参考  
1. [TCP短连接和长连接的区别](https://zhuanlan.zhihu.com/p/47074758)    



[TCP_SHORT_CONN]:/img/in-post/tcp-connection/tcp-short-conn.jpg  
[TCP_LONG_CONN]:/img/in-post/tcp-connection/tcp-long-conn.jpg 
[TCP_STATUS]:/img/in-post/tcp-connection/tcp-status.jpg 
[tcp-handshake-fin]:/img/in-post/tcp-connection/tcp-handshake-fin.jpg 


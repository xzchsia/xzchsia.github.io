---
layout:     post
title:      "理解TCP序列号（Sequence Number）和确认号（Acknowledgment Number）"
subtitle:   "TCP标志位syn,ack,fin以及序列号(seq),响应号(ack)"
date:       2020-08-31 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - TCP

---  


## 一、概念及作用  

TCP会话的每一端都包含一个32位（bit）的序列号，该序列号被用来跟踪该端发送的数据量。每一个包中都包含序列号，在接收端则通过确认号用来通知发送端数据成功接收。  

## 二、TCP三次握手  

### TCP标志位  

TCP在其协议头中使用大量的标志位或者说1位（bit）布尔域来控制连接状态，一个包中有可以设置多个标志位。  

TCP是主机对主机层的传输控制协议，提供可靠的连接服务，采用三次握手确认建立一个连接：

位码即TCP标志位，有6种标示：SYN(synchronous建立联机) ACK(acknowledgement 确认) PSH(push传送) FIN(finish结束) RST(reset重置) URG(urgent紧急)Sequence number(顺序号码) Acknowledge number(确认号码)
我们常用的是以下三个标志位：

* SYN - 创建一个连接  
* FIN - 终结一个连接  
* ACK - 确认接收到的数据  


### 握手过程  

所谓三次握手(Three-way Handshake)，是指建立一个TCP连接时，需要客户端和服务器总共发送3个包。

三次握手的目的是连接服务器指定端口，建立TCP连接,并同步连接双方的序列号和确认号并交换 TCP 窗口大小信息.在socket编程中，客户端执行connect()时。将触发三次握手。

1.第一次握手：建立连接时，客户端发送syn包(syn=j)到服务器，并进入SYN_SEND状态，等待服务器确认；

2.第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态；

3.第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。完成三次握手，客户端与服务器开始传送数据.

![TCP_3_HANDSHAKE]


#### 第一次握手：  

客户端发送一个TCP的SYN标志位置1的包指明客户打算连接的服务器的端口，以及初始序号X,保存在包头的序列号(Sequence Number)字段里。
![TCP_First_HANDSHAKE]


#### 第二次握手：  

服务器发回确认包(ACK)应答。即SYN标志位和ACK标志位均为1同时，将确认序号(Acknowledgement Number)设置为客户的I S N加1以.即X+1。

![TCP_Sec_HANDSHAKE]

#### 第三次握手：  

客户端再次发送确认包(ACK) SYN标志位为0,ACK标志位为1.并且把服务器发来ACK的序号字段+1,放在确定字段中发送给对方.并且在数据段放写ISN的+1
![TCP_Third_HANDSHAKE]


通过上面的说明，我们通过一个例子来理解  

如果你正在读这篇文章，很可能你对TCP“非著名”的“三次握手”或者说“SYN,SYN/ACK,ACK”已经很熟悉了。不幸的是，对很多人来说，对TCP的学习就仅限于此了。尽管年代久远，TCP仍是一个相当复杂并且值得研究的协议。这篇文章的目的是让你能够更加熟练的检查Wireshark中的TCP序列号和确认号

在我们开始之前，确保在Wireshark中打开示例（请到作者原文中下载）并亲自实践一下

示例中仅包含一个单独的HTTP请求，请求的流程是：web浏览器向web服务器请求一个单独的图片文件，服务器返回一个成功的响应（HTTP/1.1200 OK），响应中包含请求的文件。右键示例文件中任意一个TCP包并且选择`Follow TCP Stream`就可在单独的窗口查看原始的TCP流  
![05]  

客户端请求使用红色显示，服务端响应使用蓝色显示  
![06]  


TCP在其协议头中使用大量的标志位或者说1位（bit）布尔域来控制连接状态，我们最感兴趣的3个标志位如下：

SYN - 创建一个连接

FIN -  终结一个连接

ACK - 确认接收到的数据

就像我们看见的那样，一个包中有可以设置多个标志位

选择Wireshark中的“包”1并且展开中间面板的TCP层解析，然后展开TCP头中的标志位域，这里我们可以看见所有解析出来的TCP标志位，需要注意的是，“包1”设置了SYN标志位  
![07]  

使用同样的方式操作“包2”。可以看到"包2"设置了2个标志位：ACK - 用来确认收到客户端的SYN包，SYN - 用来表明服务端也希望建立TCP连接
![08]  

从客户端发来的“包3”只设置了ACK标志位。这3个包完成了最初的TCP3次握手  
![09]  


### 序列号和确认号：

TCP会话的每一端都包含一个32位（bit）的序列号，该序列号被用来跟踪该端发送的数据量。每一个包中都包含序列号，在接收端则通过确认号用来通知发送端数据成功接收

**当某个主机开启一个TCP会话时，他的初始序列号(Seq)是随机的，可能是0和4,294,967,295之间的任意值,这个序号的范围是0 — 2^32−1之间，而且序号的生成也是随机的，通常是一个很大的数值，也就是说每个tcp连接使用的序号也是不一样的。**，然而，`像Wireshark这种工具，通常显示的都是相对序列号/确认号，而不是实际序列号/确认号，`相对序列号/确认号是和TCP会话的初始序列号相关联的。这是很方便的，因为比起真实序列号/确认号，跟踪更小的相对序列号/确认号会相对容易一些

比如，在“包1”中，最初的相对序列号的值是0，但是最下方面板中的ASCII码显示真实序列号的值是`0xf61c6cbe`，转化为10进制为`4129057982`

如果想要关闭相对序列号/确认号，可以选择Wireshark菜单栏中的 `Edit -> Preferences ->protocols ->TCP`，去掉`Relative sequence number`后面勾选框中的√即可



## 参考  
1. [理解TCP序列号（Sequence Number）和确认号（Acknowledgment Number）](https://www.cnblogs.com/QingFlye/p/4442529.html)    
2. [TCP标志位syn,ack,fin以及序列号(seq),响应号(ack)](https://blog.csdn.net/a19881029/article/details/38091243)    
3. [20-1-tcp连接——初始化序列号(ISN)](https://blog.csdn.net/qq_35733751/article/details/80552037) 




[TCP_3_HANDSHAKE]:/img/in-post/tcp-seq-ack/01-three-handshake.png  
[TCP_First_HANDSHAKE]:/img/in-post/tcp-seq-ack/02-first-handshake.png  
[TCP_Sec_HANDSHAKE]:/img/in-post/tcp-seq-ack/03-sec-handshake.png  
[TCP_Third_HANDSHAKE]:/img/in-post/tcp-seq-ack/04-third-handshake.png
[05]:/img/in-post/tcp-seq-ack/05.png  
[06]:/img/in-post/tcp-seq-ack/06.png
[07]:/img/in-post/tcp-seq-ack/07.png
[08]:/img/in-post/tcp-seq-ack/08.png
[09]:/img/in-post/tcp-seq-ack/09.png




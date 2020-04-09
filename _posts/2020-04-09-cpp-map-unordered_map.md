---
layout:     post
title:      "c++中map与unordered_map的区别"
subtitle:   ""
date:       2020-04-09 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - C++
    - STL

---  


## 头文件  

* map: `#include <map>`
* unordered_map: `#include <unordered_map>`  


## 内部实现机理  

* map： map内部实现了一个红黑树，该结构具有自动排序的功能，因此map内部的所有元素都是有序的，红黑树的每一个节点都代表着map的一个元素，因此，对于map进行的查找，删除，添加等一系列的操作都相当于是对红黑树进行这样的操作，故红黑树的效率决定了map的效率。  
  

* unordered_map: `unordered_map`内部实现了一个哈希表，因此其元素的排列顺序是杂乱的，无序的  
  

unordered_map和map类似，都是存储的key-value的值，可以通过key快速索引到value。不同的是unordered_map不会根据key的大小进行排序，存储时是根据key的hash值判断元素是否相同，即unordered_map内部元素是无序的，而map中的元素是按照二叉搜索树存储，进行中序遍历会得到有序遍历。  

所以使用时map的key需要定义`operator<`。而unordered_map需要定义`hash_value`函数并且重载`operator==`。但是很多系统内置的数据类型都自带这些，那么如果是自定义类型，那么就需要自己重载`operator<`或者`hash_value()`了。  


## 优缺点以及适用处  

|         | map   |  unordered_map  |
| --------   | -----  | ----  |
| 优点     | 有序性，这是map结构最大的优点，其元素的有序性在很多应用中都会简化很多的操作红黑树，内部实现一个红黑书使得map的很多操作在lgnlgn的时间复杂度下就可以实现，因此效率非常的高 |   因为内部实现了哈希表，因此其查找速度非常的快     |
| 缺点        |   空间占用率高，因为map内部实现了红黑树，虽然提高了运行效率，但是因为每一个节点都需要额外保存父节点，孩子节点以及红/黑性质，使得每一个节点都占用大量的空间   |   哈希表的建立比较耗费时间   |
| 适用处        |    对于那些有顺序要求的问题，用map会更高效一些    |  对于查找问题，unordered_map会更加高效一些，因此遇到查找问题，常会考虑一下用unordered_map  |


## 结论  

如果需要内部元素自动排序，使用`map`，不需要排序使用`unordered_map`  


## note:  

对于`unordered_map`或者`unordered_set`容器，其遍历顺序与创建该容器时输入元素的顺序是不一定一致的，遍历是按照哈希表从前往后依次遍历的  



## 参考  
1. [c++中map与unordered_map的区别](https://blog.csdn.net/batuwuhanpei/article/details/50727227)    
2. [C++11 新特性： unordered_map 与 map 的对比](https://www.cnblogs.com/NeilZhang/p/5724996.html)    





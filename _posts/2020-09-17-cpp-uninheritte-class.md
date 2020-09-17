---
layout:     post
title:      "[C++] 阻止类被继承（继承类被实例化）的几种常用办法"
subtitle:   ""
date:       2020-09-17 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - C++
    - Inherit

---  


不时会留意到有人问起如何阻止 C++ 中的类被继承，但多数人都没有把这个问题问对。

在 C++11 标准之前，阻止类被继承在语法上是做不到的，大家通常做到的只是`继承而来的类不能被实例化了`。这样一来，继承得到的类就完全没有用了。虽然最终的效果一致，但对问题的理解其实是有差异的。

从 C++11 标准开始，C++ 引入了一个新的关键字 `final`，只有被 `final` 修饰的类才能真正做到不能被继承。

下面举例实现常用的以达到“不能被继承/继承类不能被实例化”的几种手段。  


## 私有化构造函数  

```c++
class Demo1
{
public:
    static Demo1* Create()
    {
        return new Demo1;
    }

private:
    // 注意：私有掉构造函数
    Demo1() {}
    Demo1(const Demo1&);
    void operator=(const Demo1&);
};

class Test1 : public Demo1
{

};

int main()
{
//  Demo1*      pd1 = new Demo1;        // 错误：不能调用私有构造
    Demo1*      pd1 = Demo1::Create();
//  Test1*      pt1 = new Test1;        // 错误：不能调用基类私有构造
}
```  

## 使用 虚继承 + 私有基类构造函数  

```c++  
template<typename T>
class SealedT
{
    // 注意：不是 friend class T;
    friend T;

private:
    SealedT() {}
};

// 注意：是虚继承
class Demo2 : virtual SealedT<Demo2>
{

};

int main()
{
    Demo2*      pd2 = new Demo2;
//  Test2*      pt2 = new Test2;        // 错误：不能访问间接虚基类
}  
```  

## 使用 虚继承 + 受保护的基类构造函数  
这个其实与前面一种形式是类似的。  
```c++  
class Sealed
{
protected:
    Sealed() {}
};

class Demo3 : virtual Sealed
{

};

class Test3 : public Demo3
{

};

int main()
{
    Demo3*      pb3 = new Demo3;
//  Test3*      pt3 = new Test3;        // 错误：不能访问间接虚基类
}  

```  

## 使用 final 关键字  

这个是最简单、最直接、最完美的。C++ 标准为啥如此晚才推出此功能。  
```c++  
class Demo4 final
{

};

// 不能从被 final 修饰的类继承，编译失败
/*
class Test4 : public Demo4
{

};
*/  

``` 



## 参考  
1. [Prevent class inheritance in C++](https://stackoverflow.com/questions/2184133/prevent-class-inheritance-in-c)    
2. [Virtual inheritance](https://en.wikipedia.org/wiki/Virtual_inheritance)    





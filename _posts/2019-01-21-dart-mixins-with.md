---
layout:     post
title:      "Dart之Mixins的with用法"
subtitle:   ""
date:       2019-01-17 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Dart  

---  

## 前言  

Mixins
混入其中(￣.￣)！

是的，Flutter 使用的是 Dart 支持 Mixin ，而 Mixin 能够更好的解决 **多继承** 中容易出现的问题，如： **方法优先顺序混乱、参数冲突、类结构变得复杂化等等。**  

Mixin 的定义解释起来会比较绕，我们直接代码从中来解释。 Dart语言的解析可以使用在线 https://dartpad.dartlang.org/ 来解决。


## With  

如下代码所示，在 Dart 中 with 就是用于 mixins。可以看出，class G extends B with A, A2 ，在执行 G 的 a、b、c 方法后，输出了 A2.a()、A.b() 、B.c() 。所以结论上简单来说，就是  
**相同方法被覆盖了，并且 with 后面的会覆盖前面的。**   

```dart  
class A {
  a() {
    print("A.a()");
  }

  b() {
    print("A.b()");
  }
}

class A2 {
  a() {
    print("A2.a()");
  }
}

class B {
  a() {
    print("B.a()");
  }

  b() {
    print("B.b()");
  }

  c() {
    print("B.c()");
  }
}

class G extends B with A, A2 {

}


testMixins() {
  G t = new G();
  t.a();
  t.b();
  t.c();
}

/// ***********************输出***********************
///I/flutter (13627): A2.a()
///I/flutter (13627): A.b()
///I/flutter (13627): B.c()

```  

接下来我们继续修改下代码。如下所示，我们定义了一个 `Base` 的抽象类，而 `A、A2、B` 都继承它，同时再 `print` 之后执行 `super()` 操作。
从最后的输入我们可以看出，`A、A2、B` 中的 **所有方法都被执行了，且只执行了一次，同时执行的顺序也是和 with 的顺序有关。**    
如果你把下方代码中 class A.a() 方法的 super 去掉，那么你将看不到 `B.a()` 和 `base a()` 的输出，这表明with语法继承从右往左的顺序依次根据当前类是否有 `super` 来决定是否调用左边一个类的同名函数。

```dart  

abstract class Base {
  a() {
    print("base a()");
  }

  b() {
    print("base b()");
  }

  c() {
    print("base c()");
  }
}

class A extends Base {
  a() {
    print("A.a()");
    super.a();
  }

  b() {
    print("A.b()");
    super.b();
  }
}

class A2 extends Base {
  a() {
    print("A2.a()");
    super.a();
  }
}

class B extends Base {
  a() {
    print("B.a()");
    super.a();
  }

  b() {
    print("B.b()");
    super.b();
  }

  c() {
    print("B.c()");
    super.c();
  }
}

class G extends B with A, A2 {

}

testMixins() {
  G t = new G();
  t.a();
  t.b();
  t.c();
}

/// ***********************输出***********************

///I/flutter (13627): A2.a()
///I/flutter (13627): A.a()
///I/flutter (13627): B.a()
///I/flutter (13627): base a()

///I/flutter (13627): A.b()
///I/flutter (13627): B.b()
///I/flutter (13627): base b()

///I/flutter (13627): B.c()
///I/flutter (13627): base c()

```  

## 总结  

上面的两种例子表明，Dart中的类继承的带有with的语法的构造顺序是从右往左的顺序执行的，with覆盖了相同的函数，即有with的情况下，相同的函数从右往左只会调用一次。  
如果添加了 `super` 调用，则会以此按照从右往左的顺序把当前有 `super` 语法的父类的同名函数调用一遍。  
比较直观的还是参看上面的两个例子来的更好理解。  

## 参考  

[Flutter完整开发实战详解(五、 深入探索)](https://juejin.im/post/5bc450dff265da0a951f032b)  

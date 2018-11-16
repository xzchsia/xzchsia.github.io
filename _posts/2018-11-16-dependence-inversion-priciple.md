---
layout:     post
title:      "依赖倒置原则"
subtitle:   "Dependence Inversion Principle"
date:       2018-11-16 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
---

## 前言  

最近抽空搞Android App， 因为自己没有这方面的经验，所以github上面找相应的别人实现的app来做参考，
所以很多app基于MVP摸索使用了Dagger2来做了很多的工作，为了加深理解，所以看了下Dagger的一些使用
和实现，发现了依赖注入这个名词，搞C++多年，好像很少听到这个名词，所以抽空研究了下依赖倒置和注入，
其实最后发现是一种设计模式，在C++中用的很多，只是不同的名词来代替，所以这里记录一下自己学习的思考。  

## 核心  

依赖倒置原则  
A. 高层次的模块不应该依赖于低层次的模块，他们都应该依赖于抽象。  
B. 抽象不应该依赖于具体实现，具体实现应该依赖于抽象。  
依赖倒置原则（Dependence Inversion Principle）是程序要依赖于抽象接口，不要依赖于具体实现。
简单的说就是要求对抽象进行编程，不要对实现进行编程，这样就降低了客户与实现模块间的耦合。  

## 代码实现及说明  

在开发过程中，目前我们有两大方案  
1. 面向过程的开发，上层调用下层，上层依赖于下层，当下层剧烈变动时上层也要跟着变动，这就会导致
模块的复用性降低而且大大提高了开发的成本。  
2. 面向对象的开发很好的解决了这个问题，一般情况下抽象的变化概率很小，让用户程序依赖于抽象，
实现的细节也依赖于抽象。即使实现细节不断变动，只要抽象不变，客户程序就不需要变化。这大大降低了
客户程序与实现细节的耦合度。  


![forexample][forexample]  

要求开发一套自动驾驶系统，只要汽车上安装该系统就可以实现无人驾驶，该系统可以在福特和本田车上使用，
只要这两个品牌的汽车使用该系统就能实现自动驾驶。于是有人做出了分析如图  
![p1class][p1class]  

```java
public class HondaCar {
    public void Run() {
        System.out.println("本田开始启动了");
    }

    public void Turn() {
        System.out.println("本田开始转弯了");
    }

    public void Stop() {
        System.out.println("本田开始停车了");
    }
}

public class FordCar {
    public void Run() {
        System.out.println("福特开始启动了");
    }

    public void Turn() {
        System.out.println("福特开始转弯了");
    }

    public void Stop() {
        System.out.println("福特开始停车了");
    }
}

public class AutoSystem {
    public enum CarType {
        Ford, Honda
    }

    ;
    private HondaCar hcar = new HondaCar();
    private FordCar fcar = new FordCar();
    private CarType type;

    public AutoSystem(CarType type) {
        this.type = type;
    }

    private void RunCar() {
        if (type == CarType.Ford) {
            fcar.Run();
        } else {
            hcar.Run();
        }
    }

    private void TurnCar() {
        if (type == CarType.Ford) {
            fcar.Turn();
        } else {
            hcar.Turn();
        }
    }

    private void StopCar() {
        if (type == CarType.Ford) {
            fcar.Stop();
        } else {
            hcar.Stop();
        }
    }
}
```  

实现针对Ford和Honda车的无人驾驶，但是软件是在不断变化的，软件的需求也在不断的变化。公司的业务做大了，
同时成为了BMW的金牌合作伙伴，于是公司要求该自动驾驶系统也能够安装在这BMW生产的汽车上。于是我们不得不变动AutoSystem  

```java
public class AutoSystem {
    public enum CarType {
        Ford, Honda, Bmw
    };

    HondaCar hcar = new HondaCar();
    FordCar fcar = new FordCar();
    BmwCar bcar = new BmwCar();
    private CarType type;

    public AutoSystem(CarType type) {
        this.type = type;
    }

    private void RunCar() {
        if (type == CarType.Ford) {
            fcar.Run();
        } else if (type == CarType.Honda) {
            hcar.Run();
        } else if (type == CarType.Bmw) {
            bcar.Run();
        }
    }

    private void TurnCar() {
        if (type == CarType.Ford) {
            fcar.Turn();
        } else if (type == CarType.Honda) {
            hcar.Turn();
        } else if (type == CarType.Bmw) {
            bcar.Turn();
        }
    }

    private void StopCar() {
        if (type == CarType.Ford) {
            fcar.Stop();
        } else if (type == CarType.Honda) {
            hcar.Stop();
        } else if (type == CarType.Bmw) {
            bcar.Stop();
        }
    }
}
```  

这会给系统增加新的相互依赖。随着时间的推移，越来越多的车种必须加入到AutoSystem中，这个“AutoSystem”模块将会被if-else语句弄得很乱，而且依赖于很多的低层模块，只要低层模块发生变动，AutoSystem就必须跟着变动，它最终将变得僵化、脆弱。  

导致上面所述问题的一个原因是，含有高层策略的模块，如AutoSystem模块，依赖于它所控制的低层的具体细节的模块（如HondaCar()和FordCar()）。如果我们能够找到一种方法使AutoSystem模块独立于它所控制的具体细节，那么我们就可以自由地复用它了。我们就可以用这个模块来生成其它的程序，使得系统能够用在需要的汽车上。面向对象给我们提供了一种机制来实现这种“依赖倒置”。

根据依赖倒置的原则，把具体的业务厂家的汽车进行抽象化，设计一个“ICar”接口，这个“AutoSystem”类不依赖于“FordCar”等具体的实现类，而依赖于抽象的ICar。所以，依赖关系被“倒置”了：“AutoSystem”模块依赖于抽象，那些具体的汽车操作也依赖于相同的抽象。  

![p2interface][p2interface]  

```java  
public interface ICar {
    void Run();

    void Turn();

    void Stop();
}

public class BmwCar implements ICar {
    public void Run() {
        System.out.println("宝马开始启动了");
    }

    public void Turn() {
        System.out.println("宝马开始转弯了");
    }

    public void Stop() {
        System.out.println("宝马开始停车了");
    }
}

public class FordCar implements ICar {
    // ...
}

public class HondaCar implements ICar {
    // ...
}

public class AutoSystem {
    private ICar icar;

    public AutoSystem(ICar icar) {
        this.icar = icar;
    }

    private void RunCar() {
        icar.Run();
    }

    private void TurnCar() {
        icar.Turn();
    }

    private void StopCar() {
        icar.Stop();
    }
}
```  
现在AutoSystem系统依赖于ICar这个抽象，而与具体的实现细节HondaCar、FordCar、BmwCar无关，所以实现细节的变化不会影响AutoSystem。对于实现细节只要实现ICar 即可，即实现细节依赖于ICar抽象。 这样后续添加新厂家的到时候，只需要实现具体的`XXXCar implements ICar`即可。


## 总结  

**抽象不应该依赖于具体，具体应该依赖于抽象。**  
得弄清楚谁依赖谁，这样才好做设计。



## 参考
1. [依赖倒置原则](https://baike.baidu.com/item/%E4%BE%9D%E8%B5%96%E5%80%92%E7%BD%AE%E5%8E%9F%E5%88%99)
2. [使用Dagger2前你必须了解的一些设计原则](https://www.jianshu.com/p/cc1427e385b5)  



[forexample]:/img/in-post/dependence-inversion-principle/for-example.jpg  
[p1class]:/img/in-post/dependence-inversion-principle/pic-1-class.jpg  
[p2interface]:/img/in-post/dependence-inversion-principle/pic-2-interface.jpg  





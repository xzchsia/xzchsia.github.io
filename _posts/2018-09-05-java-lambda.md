---
layout:     post
title:      "Java8之lambda表达式的组成及使用"
subtitle:   ""
date:       2018-09-05 
author:     "Hsia"
header-img: ""
catalog: true
keyword: MySQL
tags:
    - 技术
    - Java
    - lambda 
---

### Lambda表达式是什么？

可以把Lambda表达式理解为简洁地表示可传递的匿名函数的一种方式：它没有名称，但它有参数列表、函数主体、返回类型，可能还有一个可以抛出的异常列表。

- 匿名——我们说匿名，是因为它不像普通的方法那样有一个明确的名称：写得少而想得多！
- 函数——我们说它是函数，是因为Lambda函数不像方法那样属于某个特定的类。但和方法一样，Lambda有参数列表、函数主体、返回类型，还可能有可以抛出的异常列表。
- 传递——Lambda表达式可以作为参数传递给方法或存储在变量中。
- 简洁——无需像匿名类那样写很多模板代码。

### Lambda表达式的语法与组成

Lambda表达式由参数、箭头(->)、主体组成。如下图：
![lambda-express](http://img.hao124.net/c25961a4a7f8330c30c9774689d32bce.PNG)

- 参数列表——这里它采用了Comparator中compare方法的参数，两个Apple。
- 箭头——箭头->把参数列表与Lambda主体分隔开。
- Lambda主体——比较两个Apple的重量。表达式就是Lambda的返回值了。

所以,Lambda表达式的基本语法可以总结为:

`(parameters) -> expression` 或 `(parameters) -> { statements; }`

对照上面的语法,下表列出了一些常用的Lambda表达式:

| 使用案例	| Lambda示例  |
|-----------|-------------|
|布尔表达式 |	(List list) -> list.isEmpty() |
|创建对象 |	() -> new Apple(10) |
|消费一个对象 |	(Apple a) -> {System.out.println(a.getWeight());} |
|从一个对象中选择/抽取 |	(String s) -> s.length() |
|组合两个值 |	(int a, int b) -> a * b |
|比较两个对象	 |(Apple a1, Apple a2) ->a1.getWeight().compareTo(a2.getWeight()) |

下面是Java lambda表达式的简单例子:

```java
// 1. 不需要参数,返回值为 5 ，即使 lambda 表达式没有参数， 仍然要提供空括号，就像无参数方法一样 
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```

### 基本的Lambda例子

现在,我们已经知道什么是lambda表达式,让我们先从一些基本的例子开始。 在本节中,我们将看到lambda表达式如何影响我们编码的方式。 假设有一个玩家List, 程序员可以使用 for 语句 ("for 循环")来遍历,在Java SE 8中可以转换为另一种形式:

```java
String[] atp = {"Rafael Nadal", "Novak Djokovic",  
       "Stanislas Wawrinka",  
       "David Ferrer","Roger Federer",  
       "Andy Murray","Tomas Berdych",  
       "Juan Martin Del Potro"};  
List<String> players =  Arrays.asList(atp);  
  
// 以前的循环方式  
for (String player : players) {  
     System.out.print(player + "; ");  
}  
  
// 使用 lambda 表达式以及函数操作(functional operation)  
players.forEach((player) -> System.out.print(player + "; "));  
   
// 在 Java 8 中使用双冒号操作符(double colon operator)  
players.forEach(System.out::println);
```
正如您看到的,lambda表达式可以将我们的代码缩减到一行。
另一个例子是在图形用户界面程序中,匿名类可以使用lambda表达式来代替。

```java
// 使用匿名内部类  
btn.setOnAction(new EventHandler<ActionEvent>() {  
          @Override  
          public void handle(ActionEvent event) {  
              System.out.println("Hello World!");   
          }  
    });  
   
// 或者使用 lambda expression  
btn.setOnAction(event -> System.out.println("Hello World!"));
```

### 函数式接口

Java中已经有很多封装代码块的接口， 如 ActionListener 或 Comparator 。 lambda 表达式与这些接口是兼容的。
对于只有一个抽象方法的接口， 需要这种接口的对象时， 就可以提供一个 lambda 表达式。这种接口称为函数式接口 （functional interface )。

同样,在实现Runnable接口时也可以这样使用，下面是使用lambdas 来实现 Runnable接口 的示例:
```java
// 1.1使用匿名内部类  
new Thread(new Runnable() {  
    @Override  
    public void run() {  
        System.out.println("Hello world !");  
    }  
}).start();  
  
// 1.2使用 lambda expression  
new Thread(() -> System.out.println("Hello world !")).start();  
  
// 2.1使用匿名内部类  
Runnable race1 = new Runnable() {  
    @Override  
    public void run() {  
        System.out.println("Hello world !");  
    }  
};  
  
// 2.2使用 lambda expression  
Runnable race2 = () -> System.out.println("Hello world !");  
   
// 直接调用 run 方法(没开新线程哦!)  
race1.run();  
race2.run();
```
Runnable 的 lambda表达式,使用块格式,将五行代码转换成单行语句。


接下来,我们将使用lambdas对集合进行排序。

在Java中,Comparator 类被用来排序集合。 在下面的例子中,我们将根据球员的 name, surname, name 长度 以及最后一个字母。 和前面的示例一样,先使用匿名内部类来排序,然后再使用lambda表达式精简我们的代码。
在第一个例子中,我们将根据name来排序list。 使用旧的方式,代码如下所示:
```java
String[] players = {"Rafael Nadal", "Novak Djokovic",   
    "Stanislas Wawrinka", "David Ferrer",  
    "Roger Federer", "Andy Murray",  
    "Tomas Berdych", "Juan Martin Del Potro",  
    "Richard Gasquet", "John Isner"};  
   
// 1.1 使用匿名内部类根据 name 排序 players  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.compareTo(s2));  
    }  
});
```

使用lambdas,可以通过下面的代码实现同样的功能:
```java
// 1.2 使用 lambda expression 排序 players  
Comparator<String> sortByName = (String s1, String s2) -> (s1.compareTo(s2));  
Arrays.sort(players, sortByName);  
  
// 1.3 也可以采用如下形式:  
Arrays.sort(players, (String s1, String s2) -> (s1.compareTo(s2))); 
```

其他的排序如下所示。 和上面的示例一样,代码分别通过匿名内部类和一些lambda表达式来实现Comparator :
```java
// 1.1 使用匿名内部类根据 surname 排序 players  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.substring(s1.indexOf(" ")).compareTo(s2.substring(s2.indexOf(" "))));  
    }  
});  
  
// 1.2 使用 lambda expression 排序,根据 surname  
Comparator<String> sortBySurname = (String s1, String s2) ->   
    ( s1.substring(s1.indexOf(" ")).compareTo( s2.substring(s2.indexOf(" ")) ) );  
Arrays.sort(players, sortBySurname);  
  
// 1.3 或者这样,怀疑原作者是不是想错了,括号好多...  
Arrays.sort(players, (String s1, String s2) ->   
      ( s1.substring(s1.indexOf(" ")).compareTo( s2.substring(s2.indexOf(" ")) ) )   
    );  
  
// 2.1 使用匿名内部类根据 name lenght 排序 players  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.length() - s2.length());  
    }  
});  
  
// 2.2 使用 lambda expression 排序,根据 name lenght  
Comparator<String> sortByNameLenght = (String s1, String s2) -> (s1.length() - s2.length());  
Arrays.sort(players, sortByNameLenght);  
  
// 2.3 or this  
Arrays.sort(players, (String s1, String s2) -> (s1.length() - s2.length()));  
  
// 3.1 使用匿名内部类排序 players, 根据最后一个字母  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.charAt(s1.length() - 1) - s2.charAt(s2.length() - 1));  
    }  
});  
  
// 3.2 使用 lambda expression 排序,根据最后一个字母  
Comparator<String> sortByLastLetter =   
    (String s1, String s2) ->   
        (s1.charAt(s1.length() - 1) - s2.charAt(s2.length() - 1));  
Arrays.sort(players, sortByLastLetter);  
  
// 3.3 or this  
Arrays.sort(players, (String s1, String s2) -> (s1.charAt(s1.length() - 1) - s2.charAt(s2.length() - 1)));
```

一些常用的Lambda表达式与其对应的函数式接口应用举例：

|使用案例	|Lambda表达式	|对应的函数式接口|
|-----------|---------------|---------------|
|布尔表达式	|(List list) -> list.isEmpty()	|Predicate< List< String > >|
|创建对象	|() -> new Apple(10)|	Supplier< Apple >|
|消费一个对象|	(Apple a) -> System.out.println(a.getWeight())	|Consumer< Apple >|
|从一个对象中选择/提取|	(String s) -> s.length()|	Function< String, Integer >或ToIntFunction< String >|
|合并两个值|	(int a, int b) -> a * b	|IntBinaryOperator|
|比较两个对象|	(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())|	Comparator< Apple >或BiFunction< Apple, Apple, Integer > 或 ToIntBiFunction< Apple, Apple >|

### 方法引用

方法引用可以看作是调用特定方法的Lambda的一种快捷写法，如Apple::getWeight可以看作是Lambda表达式(Apple a) -> a.getWeight()的快捷写法。这里要表达的思想是，如果一个Lambda代表的只是“直接调用这个方法”，那最好还是用名称来调用它，而不是去描述如何调用它（Lambda表达式里包含了这种描述）。

#### 方法引用的语法
目标引用::方法的名称

示例 ： Apple::getWeight

> 注：不需要括号，Apple::getWeight()是错误的，因为没有实际调用这个方法。

几个Lambda表达式及等效方法引用的例子：

| Lambda	| 等效的方法引用  | 
|-----------|----------------|
|(Apple a) -> a.getWeight()	 | Apple::getWeight |
|() -> Thread.currentThread().dumpStack() |	Thread.currentThread()::dumpStack |
|(str, i) -> str.substring(i) |	String::substring |
|(String s) -> System.out.println(s) |	System.out::println |

#### 方法引用的分类主要有如下四类

- 指向静态方法的方法引用；  
- 指向任意类型实例方法的方法引用；  
- 指向现有对象的实例方法的方法引用；  
- 构造函数引用

##### （1）指向静态方法的方法引用

指向静态方法的引用
![指向静态方法的引用](http://img.hao124.net/3c1459370adad4aa4caa6c675ec63d88.PNG)
如：Integer的parseInt方法，写作Integer::parseInt。

##### （2）指向任意类型实例方法的方法引用

指向实例方法的引用
![指向实例方法的引用](http://img.hao124.net/eee2c7a24be6cf63221aaf76e73b8def.PNG)
如：String的length方法，写作String::length。

##### （3）指向现有对象的实例方法的方法引用

指向对象实例方法的引用
![指向对象实例方法的引用](http://img.hao124.net/a3a9169d889cb03bc441e468c4cfd239.PNG)
指向现有对象的方法引用是指在Lambda中调用了一个外部对象的实例方法，如：假设你有一个局部变量expensiveTransaction用于存放Transaction类型的对象，它支持实例方法getValue，那么你就可以写expensiveTransaction::getValue。

##### （4）构造函数引用

构造器引用与方法引用很类似，只不过方法名为 new
可以用类名加new关键字来创建构造函数的引用：ClassName::new。

> 默认构造函数

如:
```java
//构造函数引用指定默认的Apple()构造函数
Supplier<Apple> c1 = Apple::new;
//调用Supplier的get方法将产生一个新的Apple
Apple a1 = c1.get();
```
等价于下面的Lambda表达式形式：
```java
Supplier<Apple> c1 = () -> new Apple();
Apple a1 = c1.get();
```
> 带参数的构造函数

假设构造函数的签名是Apple(Integer weight)，代码如下：
```java
//引用指向Apple(Integer weight)
Function<Integer, Apple> c2 = Apple::new;
//调用Function的apply方法，产出一个指定重量的苹果
Apple a2 = c2.apply(110);
```
等价于下面的Lambda表达式形式：
```java
Function<Integer, Apple> c2 = (weight) -> new Apple(weight);
Apple a2 = c2.apply(110);
```

### 变量作用域

在Lambda表达式中使用局部变量的一些限制
在Lambda表达式中，也可以使用局部变量，就像匿名内部类在使用局部变量时需要用final修饰一样，Lambda表达式也有这个限制。像下面的代码(错误代码)：
```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
portNumber = 31337;
```
错误原因：Lambda表达式引用的局部变量必须是final变量或事实上的final变量，这里portNumber被再次修改了，显示不是final类型。

为什么会有这个限制？

局部变量保存在栈上，如果Lambda是在一个子线程中使用局部变量，则使用Lambda的线程，可能会在分配该变量的主线程将这个变量收回之后，去访问该变量。因此，Java在访问自由局部变量时，实际上是在访问它的副本，而不是访问原始变量。如果局部变量是final类型，仅仅赋值一次，那就无所谓了，因此就有了这个限制。

> 在 lambda 表达式中， 只能引用值不会改变的变量。

### 总结

在本文中,我们学会了使用lambda表达式的不同方式,从基本的示例,到使用lambdas的复杂示例。 此外,我们还学习了如何使用lambda表达式与Comparator 类来对Java集合进行排序。


### 参考文章

1. [Java中Lambda表达式的使用][ref1]
2. [java8行为参数化-逐步尝试实现代码传递][ref2]
3. [java8-Lambda表达式的组成及使用][ref3]
4. [java8中预定义的函数式接口:Predicate、Consumer、Function等][ref4]
5. [Lambda表达式的类型检查、类型推断及其带来的限制][ref5]
6. [java8方法引用-调用特定方法的Lambda的一种快捷写法][ref6]


[ref1]:https://www.cnblogs.com/franson-2016/p/5593080.html
[ref2]:http://www.hao124.net/article/88
[ref3]:http://www.hao124.net/article/89
[ref4]:http://www.hao124.net/article/91
[ref5]:http://www.hao124.net/article/92
[ref6]:http://www.hao124.net/article/93


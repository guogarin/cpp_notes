# 第十五章 面向对象程序设计

## 1. 面向对象程序设计 有哪些原则？
&emsp;&emsp; 面向对象程序设计(object-oriented programming) 的核心思想 基于三个基本概念：数据抽象、继承、动态绑定：
> 使用数据抽象，我们将类的接口和实现分离；
> 使用继承，可以对相似的关系进行建模；
> 使用动态绑定，可以在一定程度上忽略相似类的区别，并以统一的方式使用它们的对象，即在运行时选择执行函数的版本。
> 






&emsp;
&emsp;
## 2. 数据抽象
&emsp;&emsp; 用数据抽象，可以将 类的接口和实现分离，这样只需提供一个头文件给用户就行了，类的实现 对于用户来说是不可见的。






&emsp;
&emsp;
## 3. 继承
### 3.1 什么是 继承、基类、派生类？
&emsp;&emsp; 通过继承(inheritance)联系在一起的类构成一种层次关系。通常在层次关系的根部有一个基类(base class)，其它类都是 直接或间接地 从基类继承而来，这些继承而得到的类称为派生类(derived class)。

### 3.2 基类、派生类 分别负责定义哪些成员？
&emsp;&emsp; 基类负责定在在层次关系中所有类共同拥有的成员，而每个派生类定义各自特有的成员。








&emsp;
&emsp;
## 4. 动态绑定(dynamic binding)
### 4.1 什么是动态绑定？
&emsp;&emsp; 通过动态绑定(dynamic binding)，我们能用同一段代码分别处理基类和派生类对象：
我们来看下面的代码：
```cpp
// 基类
class Quote {
public:
    std::string isbn() const;
    virtual double net_price(std::size_t n) const;
};

// 派生类
class Bulk_quote : public Quote {
public:
    double net_price(std::size_t n) const override;
};

double print_total(ostream &os, const Quote &item, size_t n)
{
    // 根据 item形参的类型来确定调用  Quote::net_price() 还是 Bulk_quote::net_price()
    double ret = item.net_price(n);
    os << "ISBN: " << item.isbn() // 调用的是基类的isbn()，即 Quote::isbn
        << " # sold: " << n << " total due: " << ret << endl;
    return ret;
}
```
对于下面的调用：
```cpp
Quote basic;
Bulk_quote bulk;
print_total(cout, basic, 20); // 调用 Quote::net_price()
print_total(cout, bulk, 20); // 调用 Bulk_quote::net_price()
```
> 第一条调用语句将一个`Quote对象`传入了`print_total()`，因此当`print_total()`调用`net_price`的时候执行的是`Quote`的版本；
> 第二条调用语句将一个`Bulk_quote对象`传入了`print_total()`，因此当`print_total()`调用`net_price`的时候执行的是`Bulk_quote`的版本；
> 
在上诉的代码的运行中，函数的运行版本由实参决定，即在运行时选择函数的版本，这就叫动态绑定，也称为运行时绑定(run-time binding)

### 4.2 什么时候会发生动态绑定？
C++ primer原文：在C++语言中，当我们使用 基类的引用(或指针) 调用一个虚函数时将发生动态绑定。
&emsp;&emsp; 简单地说，虚函数是动态绑定的基础；动态绑定是实现运行时多态的基础。而要触发 动态绑定，需满足如下两个条件：
> (1)  只有虚函数才能进行动态绑定，非虚函数不进行动态绑定。
> (2)  必须通过 基类类型的引用或指针 进行函数调用。
> 
通过基类指针或基类引用做形参，当实参传入不同的派生类(或基类)的指针或引用，在函数内部触发 动态绑定，从而来 运行时 实现多态的。








&emsp;
&emsp;
## 5. 定义基类







## 虚函数(virtual function)
### 3.3 什么是虚函数(virtual function)
&emsp;&emsp; 在C++中，基类将 类型相关的函数 与 派生类不做改变直接继承的函数 区分对待。
对于某些函数，若基类希望它的派生类各自定义适合自身的版本，那基类就应该将这些函数声明为 虚函数(virtual function)

### 3.4 如何声明虚函数？
&emsp;&emsp; 在函数前面加上`virtual`即可。
```cpp
class Quote {
public:
    std::string isbn() const;
    virtual double net_price(std::size_t n) const;
};
```

### 3.5 如何继承一个类？
&emsp;&emsp; 派生类必须通过使用 类派生列表(class derivation list)明确指出它是从哪个(哪些)基类继承而来的。
&emsp;&emsp; 类派生列表的形式是：首先是一个冒号，后面紧跟以逗号分隔的基类列表，其中每个基类前面可以有访问说明符：
```cpp
class bulk_quote : public Quote {
public:
    double net_price(std::size_t n) const override;
};
```






### 虚函数、动态绑定、运行时多态之间的关系
https://blog.csdn.net/iicy266/article/details/11906509





&emsp;
&emsp;
## 参考文献


```cpp

```
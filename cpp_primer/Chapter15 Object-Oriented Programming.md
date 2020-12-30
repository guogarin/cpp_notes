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

### 4.3 C++是如何实现动态绑定的？
&emsp;&emsp; 通过虚函数。当我们使用指针或引用调用虚函数时，该调用将执行动态绑定，编译器将根据引用或指针所绑定的对象类型来决定调用对应版本的函数：该调用可能执行基类的版本(指针指向基类的对象)，也可能某个派生类的版本(指针指向派生类的对象)。








&emsp;
&emsp;
## 5. 定义基类 和 派生类
### 5.1 基类需要完成哪些工作？
(1) 基类通常都应该定义一个虚析构函数，即使该函数不执行任何实际操作也是如此。
(2) 基类必须将它的两种成员函数区分开来：一种是基类希望其派生类进行覆盖的函数；另一种是基类希望派生类直接继承而不要改变的函数。对于前者，基类应该将其定义为虚函数。
```cpp

```

### 5.2 派生类需要做哪些工作？
(1) 派生类必须使用 类派生列表 明确指出继承的是哪个基类；
(2) 派生类必须对对基类的虚函数进行重新声明和覆盖

### 5.3 基类的派生类的实例
```cpp
class Quote {
public:
    Quote() = default; 
    Quote(const std::string &book, double sales_price):
                    bookNo(book), price(sales_price) { }
    std::string isbn() const { return bookNo; }

    // returns the total sales price for the specified number of items
    // derived classes will override and apply different discount algorithms
    virtual double net_price(std::size_t n) const 
                { return n * price; }

    // dynamic binding for the destructor
    virtual ~Quote() = default; 
private:
    std::string bookNo; // ISBN number of this item
protected:
    double price = 0.0; // normal, undiscounted price
};


class Bulk_quote : public Quote { // Bulk_quote inherits from Quote
public:
    Bulk_quote() = default;
    Bulk_quote(const std::string&, double, std::size_t, double);
    // overrides the base version in order to implement the bulk purchase discount policy
    double net_price(std::size_t) const override;
private:
    std::size_t min_qty = 0; // minimum purchase for the discount to apply
    double discount = 0.0;  // fractional discount to apply
};
```
#### 5.3.1 在上面的例子中，类`Bulk_quote`有哪些成员？
**自己定义的**：
&emsp; 成员函数：`net_price()`
&emsp; 数据成员：`discount`、`min_qty`
**从基类继承的：**
&emsp; 成员函数：`isbn()`
&emsp; 数据成员：`bookNo`、`price`
**注意：**`bookNo成员`是基类的私有成员，虽然派生类`Bulk_quote`从基类继承了该成员，但是不能通过常规手段访问它。





&emsp;
&emsp;
## 7. 派生类能否访问基类的私有成员？
**不能直接访问：**
&emsp;&emsp; 派生类是不能直接访问基类的`private`成员的，要是可以访问的话就不需要引入`protected`关键字了。
来看下面的代码：
```cpp
class base{
public:
    base(int a = 6):sum(a) { };
private:
    int sum;
};

class son:public base{
public:
    son()= default;
private:
    int number;
};


int main(void)
{
    son s;
    cout << s.sum << endl;
    return 0;
}
```
编译的时候报错了
<div align="center"> <img src="./pic/chapter15/访问基类的私有成员.png"> </div>
<center> <font color=black> <b> 图1 访问基类的私有成员的报错信息 </b> </font> </center>

**但是可以通过成员函数来访问：**
```cpp
class base{
public:
    base(int a = 6):sum(a) { };
    int get_sum(){return sum; }
private:
    int sum;
};

class son:public base{
public:
    son()= default;
private:
    int number;
};


int main(void)
{
    son s;
    cout << "sum : " << s.get_sum() << endl;
    return 0;
}
```
输出结果：
> sum : 6
> 






&emsp;
&emsp;
##  8. 如果基类中未提供访问私有成员的`public`函数，那在派生类中这些基类的私有成员是否 “存在“ 呢？还会不会被继承呢？？
&emsp;&emsp; 其实在派生类中，这些基类的私有成员的确是存在的，而且会被继承，只不过程序员无法通过正常的渠道访问到它们。
考察如下程序，通过一种特殊的方式访问了类的私有成员。
```cpp
class base{
public:
    base(int a = 6):sum(a) { };
    int get_sum(){return sum; }
private:
    int sum;
    void private_printer(){cout << "你好，我是基类的私有成员函数"<<endl;}
};

class son:public base{
public:
    son()= default;
    void print_base(){
        int* p = reinterpret_cast<int*>(this);//
        cout << *p << endl;
    }   
    
//    void usePrivateFunction(){
//        void(*func)()=NULL;
//        asm("mov eax,A::privateFunc;mov func,eax;");
//        func();
//    }
private:
    int number;
};

int main(void)
{
    son s;
    s.print_base();
    //s.usePrivateFunction();
    return 0;
}
```
输出结果：
> 6
> 
由上面的结果可知：虽然`基类base`没有提供访问`私有成员变量sum`的公有方法，但是在`派生类son`的对象中包含了`基类的私有成员变量sum`
&emsp;&emsp; 综上所述，类的私有成员一定存在，也一定被继承到派生类中。只不过收到C++语法的限制，在派生类中访问基类的私有成员只能通过间接的方式进行。






&emsp;
&emsp;
## 9. 单继承
&emsp;&emsp; 大多数类都只继承自一个类，这种形式的继承被称为 单继承。






&emsp;
&emsp;
## 10. 为什么需要`protected`？
&emsp;&emsp; 派生类可以继承那些定义在基类中的成员，但派生类的成员函数不能访问基类的私有成员，若基类希望某些成员可以被派生类所访问但不能被其它用户访问，可以将该成员声明为`protected`
| 关键字      | 作用                       |
| ----------- | -------------------------- |
| `public`    | 所有用户都能访问           |
| `private`   | 仅友元和类本身可以访问     |
| `protected` | 子类、友元和类本身可以访问 |






&emsp;
&emsp;
## 11. 一个派生类对象由哪几个部分组成？
&emsp; 一个派生类对象包含多个组成部分：
> &emsp;&emsp;① 一个含有派生类自己定义的非静态成员的子对象，
> &emsp;&emsp;② 一个与该派生类继承的基类对象的子对象
> 
就拿前面定义过的 派生类`Bulk_quote` 和基类`Bulk_quote` 来说，虽然C++标准没有明确规定派生类的对象在内存中如何分布，但是我们可以认为 派生类`Bulk_quote`的队形包含下图的两个部分：
<div align="center"> <img src="./pic/chapter15/Bulk_quote对象的概念结构.png"> </div>
<center> <font color=black> <b> 图2 Bulk_quote对象的概念结构 </b> </font> </center>





&emsp;
&emsp;
## 12. 派生类及其对象 和 基类 之间的类型转换
### 12.1 为什么  派生类及其对象 可以向 基类 转换？
&emsp;&emsp; 在前面以及提到过 一个派生类 可以分为 派生类自身的部分 和 该派生类对应的基类部分，而之所以存在派生类向基类的类型转换是因为每个派生类对象都包含一个基类的部分，**而将一个基类的指针(引用)指向 其派生类的对象 的本质其实就是** 将 该基类的指针 指向 该派生类的基类部分，所以我们可以把派生类的对象当成基类的对象来使用。
在下面的代码中，其实就是让 基类引用`r` 指向了 派生类对象`bulk`的基类部分：
```cpp
Bulk_quote bulk; // bulk 是派生类对象
Quote &r = bulk; // r 是指向指向基类的引用，这里的结果是 r指向 派生类对象bulk 中的 基类部分
```

### 12.2 如何进行 派生类及其对象 到 基类 的类型转换？
&emsp;&emsp; 这种转换通常被称为**派生类到基类的类型转换**，和其它类型转换一样，编译器会隐式的执行这种类型转换。这种隐式特性意味着我们可以把 **派生类对象的引用(指针)** 用在需要 基类引用(指针) 的的地方：
```cpp
Quote item; // item 是基类对象
Bulk_quote bulk; // bulk 是派生类对象
Quote *p = &item; // p 是指向基类的指针
p = &bulk; // p 是指向基类的指针，但也能让它指向派生类对象（准确的说是指向 派生类中的 基类部分）
Quote &r = bulk; // r 是指向指向基类的引用，这里的结果是 r指向 派生类对象bulk 中的 基类部分
```

### 13.3 如何把一个从一个基类 转换为 它的派生类？
&emsp;&emsp;.TODO: c++ primer P534






&emsp;
&emsp;
## 14. 派生类的构造函数
### 14.1 派生类如何初始化从基类中继承的成员？
&emsp;&emsp; 对于那些从基类中继承而来的成员，派生类是不能直接初始化的，派生类必须通过调用基类的构造函数来初始化它的基类部分，**简而言之就是** 每个类控制它自己的成员的初始化过程。
我们现在来完成上面派生类`Bulk_quote`的构造函数：
```cpp
Bulk_quote(const std::string& book, double p,
            std::size_t qty, double disc) :
                Quote(book, p), min_qty(qty), discount(disc) { }
```
#### 14.1.1 上面的构造函数`Bulk_quote::Bulk_quote()`的执行过程是？
其实就是按顺序执行，先基类后派生类，而构造函数都是先执行它的初始化列表，然后再执行函数体，但很多构造函数的函数体都是空的，具体过程如下：
> (1) 构造函数`Quote()`的 构造函数初始化列表;
> (2) 构造函数`Quote()`的 函数体(这里是空的)；
> (3) 构造函数`Bulk_quote`的 构造函数初始化列表;
> (4) 构造函数`Bulk_quote`的 函数体(这里是空的)；
> 
### 14.2 如果派生类没有显示调用基类的构造函数对继承自基类成员进行初始化 会发生什么？
该派生类对象的基类部分将进行默认初始化。



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

### 虚函数 和 非虚函数的成员函数 有什么区别？
&emsp; 差别主要在 该成员函数的解析过程发生在何时：
> &emsp;&emsp; 虚函数 ：发生在 运行时；
> &emsp;&emsp; 非虚函数：发生 在编译时

### 如果派生类没有覆盖它继承的虚函数 会发生什么？
&emsp;&emsp; 如果派生类没有覆盖其基类中的某个虚函数，则该虚函数的行为类似于其它的普通成员，即派生类会直接继承其在基类中的版本。

#### 如何覆盖基类的虚函数？
(1) 派生类可以在它覆盖的函数前使用`virtual`关键字，但这不是必须的
(2) C++11允许派生类显式的注明它使用某个成员函数覆盖它继承的虚函数，即在形参列表后面、const成员函数的const关键字后面、或者引用成员函数的引用限定符后面 添加一个关键字`override`



&emsp;
&emsp;
## 覆盖(override)

```cpp

```


&emsp;
&emsp;
## 类派生列表中的 访问说明符 的作用是？

```cpp

```


### 虚函数、动态绑定、运行时多态之间的关系
https://blog.csdn.net/iicy266/article/details/11906509


## C++ 类在内存中的存储方式
https://zhuanlan.zhihu.com/p/103384358
https://zhuanlan.zhihu.com/p/103490276


&emsp;
&emsp;
## 参考文献
1. [C++中基类私有成员会被继承吗](https://cloud.tencent.com/developer/article/1177138)
2. [C++ 类在内存中的存储方式](https://zhuanlan.zhihu.com/p/103384358)
```cpp

```
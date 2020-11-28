
# 第十三章 拷贝控制

## 1. 拷贝控制操作
### 1.1 有哪些拷贝控制操作？
拷贝构造函数(copy constructor)
拷贝赋值运算符(copy assignment operator)
移动构造函数(move constructor)
移动赋值运算符(move assignment operator)
析构函数(destructor)
### 1.2 这些拷贝控制操作各自的作用分别是什么？
拷贝构造函数、移动构造函数定义了: 当用同类型的另一个对象 初始化本对象时 做什么。
拷贝赋值运算符、移动赋值运算符定义了: 将一个对象 赋予同类型的另一个对象时 做什么。
析构函数定义了:当此类型对象销毁时做什么。
### 1.3 为什么要自己定义 控制操作？
&emsp;&emsp; 如果我们不自己显示定义这些操作，那编译器会帮我们定义，但问题是编译器定义的版本的行为并不是我们想要的，依赖这些编译器定义的操作有可能会造成灾难。






&emsp;
## 2. 拷贝构造函数
### 2.1 拷贝构造函数 在什么时候被调用？
&emsp;&emsp; 当用同类型的一个对象 **初始化** 另一个对象时（注意是初始化哦）
### 2.2 什么样的函数是 拷贝构造函数？
**书上的定义：** 如果一个构造函数的第一个参数是自身类类型的引用，且任何额外的参数都有默认值，则此构造函数是 拷贝构造函数。
从上述定义可以总结出  拷贝构造函数 有如下几个特点：
> ① 首先要是构造函数（即函数名为类名）；
> ② 第一个参数必须是 自身类类型的引用（注意是引用哦）；
> ③ 如果有其它参数，则必须有默认值。

下面分别是什么构造函数？
```cpp
class Foo {
public:
    Foo();                          // 默认构造函数
    Foo(const Foo&);                // 拷贝构造破函数
    Foo(int size);                  // 普通的构造函数
    Foo(const Foo&, int size);      // 普通的构造函数
    Foo(const Foo&, int size = 0);  // 拷贝构造函数
    ~Foo();                         // 析构函数
    // ...
};
```

### 2.3 拷贝构造函数 可以是 explicit的吗？
&emsp;&emsp; 拷贝构造函数检查会被 隐式地使用，因此拷贝构造函数通常不应该声明为explicit

### 2.4 拷贝构造函数 的首元素必须是const的吗？
&emsp;&emsp; 不是必须，虽然我们可以定义一个 非const引用 的拷贝构造函数，但是基于 `对于那些不会改变的变量，都尽量声明为const`的原则，因此还是声明为const比较好。

### 2.5 如果没有自己定义 拷贝构造函数，会发生什么？
&emsp;&emsp; 编译器会为我们生成 **合成拷贝构造函数**

### 2.6 合成默认构造函数 和 合成拷贝构造函数 的生成规则有何不同？
&emsp;&emsp; **合成默认构造函数** 只有在 类里没有任何构造函数时，编译器才会帮忙生成；而**合成拷贝构造函数**就不一样了，只要没有自己定义 拷贝构造函数，即使你定义了其它 构造函数，编译器也会帮你生成 合成拷贝构造函数。
我们来看下面的代码：
```cpp
class Foo_1 {
public:
    Foo_1(int size);                  // 普通的构造函数
};

class Foo_2 {
public:
    
};
```
在上面的代码中，编译器只会为类`Foo_1`生成 合成拷贝构造函数，但是会为 `Foo_2`生成 合成默认构造函数 和 合成拷贝构造函数 

### 2.7 合成拷贝构造函数 有什么作用？
有两个作用：
> (1) 一般情况下，合成拷贝构造函数 会将其参数的成员逐个赋值到正在创建的对象中去；
> (2) 对于某些类来说，合成拷贝构造函数 是用来 **阻止我们拷贝该类类型的对象** 的。

### 2.8 合成拷贝构造函数 的拷贝规则是怎样的？
针对不同类型的对象，合成拷贝构造函数 的拷贝规则 有所不同：
> **内置类型：** 直接拷贝；
> **类对象：** 使用该类的拷贝构造函数来拷贝；
> **数组：** 虽然我们不能拷贝数组，但合成拷贝构造函数会逐个拷贝数组中的对象，数组元素若是内置类型则直接拷贝，若是类类型，则 使用该类的拷贝构造函数来拷贝。

### 2.9 拷贝初始化 和 直接初始化区别:
&emsp;&emsp; **直接初始化:** 实际上是 要求编译器 使用普通的函数匹配规则 来选择与我们的参数最匹配的**构造函**数;
&emsp;&emsp; **拷贝初始化:** 实际上是 要求编译器 将右侧的对象拷贝到 正在创建的对象中,如果需要还要进行类型转换(先调"构造函数"匹配建立左侧对象->调 "拷贝构造函数/移动拷贝构造函数" 将右侧对象拷贝);

### 2.10 拷贝初始化一般在什么时候发生？
拷贝初始化通常使用拷贝构造函数来完成。拷贝构造函数不仅在我们用等号`=`定义变量时调用，在下列情况下也会调用：
> (1) 根据另一个同类型的对象显式或隐式初始化一个对象；
> (2) 函数调用时，将一个对象作为实参 传递给一个非引用类型的形参；
> (3) 从一个返回类型为非引用类型的函数返回一个对象；
> (4) 用花括号列表初始化一个数组中的元素或一个聚合类中的成员；
> (5) 标准库容器初始化，或者调用insert或push成员时，容器会对其元素进行拷贝初始化（与之相对的是，emplace成员创建的元素都是直接初始化）。

### 2.11 拷贝初始化 通过什么来完成？
拷贝构造函数 或 移动构造函数：
> 通常是使用 **拷贝构造函数** 来完成；
> **但如果一个类有一个移动构造函数**，则拷贝初始化有时会使用移动构造函数来完成。

### 2.12 拷贝构造函数 首对象必须是引用？
&emsp;&emsp; 是的，必须是引用。
&emsp;&emsp; 如果拷贝构造函数中的参数不是一个引用，那么就相当于采用了传值的方式(pass-by-value)，而传值的方式会调用该类的拷贝构造函数，从而造成无穷递归地调用拷贝构造函数。因此拷贝构造函数的参数必须是一个引用。

### 2.13 拷贝构造函数不能传值， 但可以传指针吗？
&emsp;&emsp; 可以自己定义一个以指针为参数的构造函数，并且自己在相应的情况下可以使用，但程序不会在需要拷贝构造函数时自动调用它。

### 2.14 explicit构造函数 和 拷贝初始化
&emsp;&emsp; 因为explicit构造函数 不能
```cpp
vector<int> v1(10);     // ok: direct initialization
vector<int> v2 = 10;    // error: constructor that takes a size is explicit
void f(vector<int>);    // f's parameter is copy initialized
f(10);                  // error: can't use an explicit constructor to copy an argument
f(vector<int>(10));     // ok: directly construct a temporary vector from an int
```

### 2.15 编译器可以绕过拷贝构造函数
.TODO: 不是很明白书上想表达的意思，后面回来再看。






&emsp;
## 3. 拷贝赋值运算符
### 3.1 拷贝赋值运算符 在什么时候被调用？
&emsp;&emsp; 将一个对象 赋予 同类型的另一个对象时（注意是赋值哦）

### 3.2 拷贝赋值运算符 和 拷贝构造函数 的作用有何不一样？
拷贝赋值运算符 管的是 用 `=` 赋值
拷贝构造函数 管的是 拷贝初始化
```cpp
Sales_data trans, accum;
tranc = accum;  // 此时调用的是 拷贝赋值运算符

Sales_data a;
Sales_data b = a;// 此时调用的是 拷贝构造函数
```

### 3.3 为什么需要 拷贝赋值运算符？
&emsp;&emsp; 拷贝构造函数只能在初始化进行状态的赋予，若需要在初始化后再进行状态整体的赋予需要用到赋值构造函数。

### 3.4 如果没有自己定义 拷贝赋值运算符，会发生什么？
&emsp;&emsp; 编译器会为我们合成一个。

### 3.5 如何使用 拷贝赋值运算符？
&emsp;&emsp; 就和正常的赋值一样使用就行。

### 3.6 有等号`=`的时候一定是拷贝赋值运算符吗？
&emsp;&emsp; 等号`=`不一定是拷贝赋值运算符，发生在初始化时可能是拷贝构造函数或移动构造函数。

### 3.7 如何为类定义 自己的 拷贝赋值运算符？
&emsp;&emsp; 重载赋值运算符即可。
&emsp;&emsp; 重载运算符本质上是函数，其名字由`operator`关键字后接要定义的运算符号，如：
```cpp
class Foo{
public:
    // 返回类型为自己的引用
    // `operator`关键字后接要定义的运算符号，这里为 =
    Foo& operator=(const Foo&);
}
```

### 3.8 定义 自己的 拷贝赋值运算符 时要注意什么？
&emsp;&emsp; 为了和内置类型的赋值保持一致，重载赋值运算符时 通常返回一个 指向其左侧运算对象的引用。(内置类型的赋值运算符为什么要返回 左侧运算对象的引用 的原因见第四章的笔记)

### 3.9 拷贝赋值运算符 和 标准库 容器
&emsp;&emsp; 标准库通常要求 保存在容器中的元素类型 要具有 赋值运算符，而且返回值是左侧运算对象的引用。

### 3.9 对于下面的类定义，编译器生成的 合成拷贝运算符执行的操作是？
```cpp
class Sales_data {
public:
    /*  由于我们已经定义了其它形式的构造函数，因此编译器不会为我们合成默认构造函数；
    因此使用 `=default` 来要求编译器生成默认构造函数  */
    Sales_data() = default; 

    Sales_data(const std::string &s, unsigned n, double p):
                bookNo(s), units_sold(n), revenue(p*n) { }
    Sales_data(const std::string &s): bookNo(s) { }
    Sales_data(std::istream&);
    std::string isbn() const { return bookNo; }
    Sales_data &combine(const Sales_data&);
private:
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
};
```
很明显，上面的类没有定义自己的 拷贝赋值运算符，因此编译器会为其合成一个，这个 合成拷贝赋值运算符执行的操作相当于下面这个函数：
```cpp
// equivalent to the synthesized copy-assignment operator
Sales_data&
Sales_data::operator=(const Sales_data &rhs)
{
    bookNo = rhs.bookNo;            // bookNo成员是string类型，因此调用的是 string::operator=
    units_sold = rhs.units_sold;    // 使用内置的int赋值
    revenue = rhs.revenue;          // 使用内置的double赋值
    return *this;       // 返回一个自身的引用
}
```





&emsp;
## 4. 析构函数
### 4.1 析构函数的作用是？
&emsp;&emsp; 析构函数 执行和构造函数相反的操作：释放对象资源，析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。

### 4.2 析构函数 有何特点？
名字由波浪线接类名构成；没有返回值，也不接受参数：
```cpp
class Foo {
public:
    ~Foo(); // destructor
    // ...
};
```

### 4.3 一个类能由几个析构函数？
&emsp;&emsp; 只能有一个，因为析构函数不接受参数，所以它不能被重载，因此对于一个类来说，只会有一个析构函数。

### 4.4 在析构函数中，成员的析构在什么时候？顺序是怎样的？
&emsp;&emsp; 和构造函数相反，首先执行函数体，然后再销毁成员，而且成员按初始化的顺序的**逆序**销毁。

### 4.5 析构函数在什么时候被调用？
&emsp;**对析构函数可以先这么理解：“对象生命周期结束时自动调用的函数”**
&emsp;无论何时一个对象被**销毁**，就会自动调用其析构函数：
> &emsp;变量在离开其作用域时被销毁；
> &emsp;容器（无论是标准库容器还是数组）被销毁时，其元素被销毁；
> &emsp;对于动态分配的对象，当对 指向它的指针使用`delete`运算符时被销毁；
> &emsp;对于临时对象，当创建它的完整表达式结束时被销毁；

```cpp
{   // 新作用域
    // p 和 p2 指向动态分配的对象
    Sales_data *p = new Sales_data;         // p 是内置指针
    auto p2 = make_shared<Sales_data>();    // p2 是一个 shared_ptr
    Sales_data item(*p);        // 拷贝构造函数 将*p拷贝到item
    vector<Sales_data> vec; // 局部变量
    vec.push_back(*p2);     // 拷贝p2指向的对象到vec
    delete p;   // 对 p 指向的对象执行 析构函数
}   // 退出局部作用域, 对item、p2、vec调用析构函数
    // 若 p2 的引用计数变为 0, 则对象将被释放
    // 销毁 vec 会销毁它里面的元素
```
在上面的代码中：
&emsp;&emsp; 我们需要直接管理的只有指针`p`指向的对象，因为它是 动态分配的Sales_data对象，因此在退出局部作用域前必须通过`delete`释放其内存，要不然就会造成内存泄露，但 `delete p` 这个语句是怎么释放对象的呢？ 对于对于动态分配的对象，当对 指向它的指针使用`delete`运算符时，该对象的类型的析构函数将被调用，然后释放该对象。
继续来看下面的代码：
&emsp;&emsp; 其余的对象 `item、p2、vec`都会在离开局部作用域后自动调用其析构函数进行，意味着在这些对象上会分别执行 `Sales_data`、`shared_ptr`、`vector`的析构函数。

```cpp
#include <iostream>
 
using namespace std;
 
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line();   // 这是构造函数声明
      ~Line();  // 这是析构函数声明
 
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
 
void Line::setLength( double len )
{
    length = len;
}
 
double Line::getLength( void )
{
    return length;
}
// 程序的主函数
int main( )
{
   Line line;
 
   // 设置长度
   line.setLength(6.0); 
   cout << "Length of line : " << line.getLength() <<endl;
 
   return 0;
}
```
结果：
> Object is being created
> Length of line : 6
> Object is being deleted

### 4.6 下面的代码的运行原理是？
```cpp
#include <string>
#include <iostream>

using namespace std;

class B
{
public:
    B(int a = 10, int b = 10, string s = "Hello World"): num(a), total(b), str(s) {}
    ~B(){
        cout << "I am the destructor of class B" << endl;
    }   
    
    int num;
    int total;
    string str; 
    
};

class D
{
public:
    ~D() = default;
    B m;
};


int main()
{
    D demo;
    demo.~D();

    cout << demo.m.num << endl << demo.m.total << endl << demo.m.str << endl;

    return 0;
}
```
结果：
> I am the destructor of class B
> 10
> 10
> Hello World
> I am the destructor of class B

#### &emsp; 4.6.1 为什么被析构后还能继续访问 类D 的成员 m？
调用demo.~D()之后所有内存都释放掉了，后续的：
        `cout << demo.m.num << endl << demo.m.total << endl << demo.m.str << endl;`
虽然还能打印出结果，但实际上已经属于越界访问，是有问题的代码，是未定义行为。

#### &emsp;4.6.2 为什么 B类 的析构函数被调用两次？
&emsp;&emsp; 第一次 是显示调用，即`demo.~D();`
&emsp;&emsp; 第二次 是编译器隐式调用，因为变量`demo`离开其作用域(main函数)后被销毁，编译器自动调用其析构函数。

### 4.7 可以显示调用析构函数吗？
&emsp;&emsp; 不建议这么做，因为当对象离开其作用域时，编译器会隐式调用其析构函数，如果前面自己显示调用了的话，那么就是调用了两个析构函数，如果申请了堆内存(动态内存)，那么就是 对其进行了两次`delete`，后果很严重。

### 4.8 成员在什么时候被销毁？
&emsp;&emsp; 在**析构函数体** 执行完毕后，成员被销毁。
&emsp;&emsp; 认识到 析构函数体 自身 并不直接销毁成员是非常重要的，成员是在析构函数体之后隐含的析构阶段中被销毁的，在整个对象销毁的过程中，析构函数体是作为成员销毁步骤之外的另一部分而进行的。
### 4.9 什么时候不能依赖编译器生成的 合成析构函数 ？
&emsp;&emsp; 因为合成析构函数 不会 释放动态内存，因此当类中分配了动态内存时 不能依赖合成析构函数，而要自己定义一个析构函数来 delete动态内存。






&emsp;
## 5. 三五法则
### 5.1 为什么叫三五法则？
&emsp;&emsp; 由于拷贝控制操作是由三个特殊的成员函数来完成的，所以我们称此为“C++三法则”。在较新的 C++11 标准中，为了支持移动语义，又增加了移动构造函数和移动赋值运算符，这样共有五个特殊的成员函数，所以又称为“C++五法则”。
&emsp;&emsp; 也就是说，“三法则”是针对较旧的 C++98 标准说的，“五法则”是针对较新的 C++11 标准说的。为了统一称呼，后来人们把它叫做“C++ 三/五法则”。
### 5.2 自定义类的这些操作时应该遵循什么样的原则？原因是什么？
**(1) 需要自定义析构函数的类 也需要自定义 拷贝构造函数和拷贝赋值运算符**
&emsp;&emsp; 我们都知道，合成析构函数不会delete一个指针元素成员，因此在类中分配了动态内存时需要自定义析构函数来释放动态内存，我们来看`HasPtr`类的定义，它分配了动态内存，且自定义了析构函数来delete动态内存：
```cpp
class HasPtr {
public:
    HasPtr(const std::string &s = std::string()):ps(new std::string(s), i(0)) { };
    ~HasPtr() {delete ps;} 
    // 其它类成员
private:
    std::string *ps;
    int i;
}
```
`HasPtr`类看起来是没有任何问题的：它分配了动态内存，且在析构函数中对其进行了`delete`，避免了内存泄露，接下来我们来使用一下这个类：
```cpp
HasPtr f(HasPtr hp)
{
    HasPtr ret = hp; // 拷贝指定的 HasPtr
    // 处理ret
    return ret;
}
```
上面的代码中涉及到了两个`HasPtr`局部变量：实参hp和变量ret，在函数`f()`结束后它们将通过`HasPtr`的析构函数被释放，但是`HasPtr`类没有自定义 拷贝构造函数和拷贝赋值运算符，而是依赖编译器的合成版本，而编译器的合成版本仅仅是简单的拷贝指针成员，这导致多个指针指向了相同的动态内存，这就导致在析构 实参hp和变量ret 时，`delete`的是相同的内存，这显然是一个错误。
**如何解决上面的问题：** 自定义拷贝构造函数和拷贝赋值运算符，不仅仅是拷贝 指向动态内存的指针，而是对里面的内容也进行拷贝，这样就不会造成一块内存被`delete`多次的现象。
**总结**：
&emsp;&emsp; 如果一个类需要自定义析构函数，几乎可以肯定的是它也需要自定义拷贝构造函数和拷贝赋值运算符。
**(2) 需要拷贝操作的类 也需要赋值操作，反之亦然**
&emsp; 考虑这样一个`number`类：它为每个对象分配一个 独有的、唯一的序号，那这个类就不能使用合成版本的 拷贝构造函数和拷贝赋值运算符：
&emsp;&emsp; **若使用合成本的 拷贝构造函数：**`number new_num_1 = num;`，合成拷贝运算符 将会简单的赋值`num`变量中的序号给`new_num_1`，这造成`num`和`new_num_1`中的序号是一样；
&emsp;&emsp;** 若使用合成本的 拷贝赋值运算符：**`number new_num_2(num);`，合成拷贝构造函数 将会简单的赋值`num`变量中的序号给`new_num_1`，这造成`num`和`new_num_1`中的序号是一样；
**总结：**合成版本的拷贝构造函数和拷贝赋值运算符 仅仅是简单的拷贝，这就会造成新构造(拷贝)的类会和 给定对象的序号是一样，这不满足类的设计要求，因此需要自己定义 自定义 拷贝构造函数和拷贝赋值运算符 来完成新对象的构造，以满足每个对象的序号 是唯一的。
### 5.3 需要 自定义 拷贝构造函数和拷贝赋值运算符 的类 是否一定需要 自定义 析构函数？
&emsp;&emsp; 需要自定义析构函数的类 也需要自定义 拷贝构造函数和拷贝赋值运算符，但反过来却不一定。






&emsp; 
## 6. 显示要求编译器生成合成版本的 拷贝控制成员
### 6.1 如何 显示要求编译器生成合成版本的 拷贝控制成员？
&emsp;&emsp; 使用`=default`：
```cpp
class Sales_data {
public:
    Sales_data(const Sales_data&) = default;
    Sales_data& operator=(const Sales_data &) = default;
    Sales_data() = default;
    ~Sales_data() = default;
}
```
**注意：** 当我们在 类内 用`=default`修改成员声明时，合成的函数将**隐式**地被声明为内联的（就像任何其他类内声明的成员函数一样）。

### 6.2 如果希望 编译器生成合成版本的 拷贝控制成员 不是内联的应该怎么做？
&emsp;&emsp; 当我们在 类内 用`=default`修改成员声明时，合成的函数将**隐式**地被声明为内联的（就像任何其他类内声明的成员函数一样），如果不希望合成的成员是内联的，我们应该只对成员的 类外**定义**使用`=default`：
```cpp
class Sales_data {
public:
    Sales_data(const Sales_data&) = default;
    Sales_data& operator=(const Sales_data &);
    Sales_data() = default;
    ~Sales_data() = default;
}
Sales_data& Sales_data::operator=(const Sales_data &) = default;
```
在上面的代码中，拷贝控制运算符 不是内联的，其它控制成员都是。

### 6.3 什么类型的 函数可以使用`=default`？
&emsp;&emsp; 我们只能对 具有合成版本的 成员函数 使用`=default`（即 默认构造函数、拷贝控制成员）





&emsp; 
## 7. 阻止拷贝
### 7.1 为什么要阻止拷贝？
&emsp; &emsp; 虽然大部分类应该定义拷贝构造函数和拷贝赋值运算符，但对于某些类来说，这些操作没有任何意义，比如说`iostream`类阻止了拷贝(因为流类型不允许拷贝)，以避免多个对象写入或读取相同的IO缓冲。

### 7.2 如何阻止拷贝
&emsp;&emsp; 为了阻止拷贝，我们似乎应该直接不定义 拷贝控制成员就行了，但如果你不定义，编译器就会帮你生成。
&emsp;&emsp; 如何解决这个问题呢？ 在C++11新标准下，我们可以通过将 拷贝构造函数、拷贝赋值运算符定义为 删除的函数(deleted function)来阻止拷贝。

### 7.3 什么是 删除的函数？
&emsp;&emsp; 我们虽然声明了它们，但不能以任何方式使用它们

### 7.4 如何定义 删除的函数？
&emsp;&emsp; 通过在函数的参数列表后面加上`=delete`来指出我们希望该函数被定义为 删除的。

### 7.5 定义一个 NoCopy类，这个类使用 默认构造函数、析构函数用合成的，并阻止拷贝
```cpp
class NoCopy {
public:
    NoCopy() = default;
    NoCopy(const NoCopy &)=delete;
    NoCopy & operator=(const NoCopy &) = delete;
    ~NoCopy() = default;
}
```

### 7.6 将函数声明为`=default` 和 `=delete` 时 有何不同？ 为什么？
(1) `=default`既可以和声明一起出现在类的内部，也可以作为定义出现在类的外部，而`=delete`必须出现在函数第一次声明的时候，原因如下：
&emsp;&emsp; 一个默认成员只影响为这个成员生成的代码，因此`=default`直到编译器生成代码的时候才需要；
&emsp;&emsp; 而另一方面，编译器需要知道一个函数是否为删除的，以便从一开始就禁止使用这个被删除的函数
(2) 我们可以对任何函数指定`=delete`，但只能对 具有合成版本的 成员函数 使用`=default`（即 默认构造函数、拷贝控制成员）

### 7.7 哪些函数不能是 删除的成员？为什么？
&emsp;&emsp; 析构函数不能是删除的，如果析构函数被删除了，那就无法销毁此类型的对象了。

### 7.8 如果一个类的析构函数被删除了，会发生什么？
&emsp;&emsp; 析构函数不能被删除，但如果非要将析构函数定义为删除的，那编译器将不允许定义该类型的变量 或 创建该类的临时对象。
&emsp;&emsp; 而且，如果一个类 有某个成员的析构函数是被删除的，我们也不能定义该类型的变量或临时对象，因为如果一个成员的析构函数是被删除的，那该成员就无法销毁，该成员不能被销毁，则整个成员也就无法销毁了。    

### 7.9 如果一个类的析构函数被删除了，是不是就不能创建任何该类的对象了？
&emsp;&emsp; 不是，如果一个类的析构函数被删除了，我们不能定义该类的变量或成员，但我们可以动态分配这种类型的对象，不过我们不能释放这种对象，即不能`delete`它：
```cpp
struct NoDestructor {
    NoDestructor() = default;   // 使用合成默认构造函数
    ~NoDestructor() = delete;   // 删除 析构函数
}

NoDestructor nd; // 错误，不能创建 析构函数被删的的类 的对象
NoDestructor * ptr = new NoDestructor(); // 正确，但我们不能delete ptr
delete ptr;         // 错误，NoDestructor的析构函数是delete的
```
### 7.10 一个类的 合成版本的控制成员 什么时候会是 删除的？为什么？
&emsp;&emsp; (1) 如果 类的某个成员的析构函数 是删除的 或 不可访问的(例如是`private`)，则该类的 合成析构函数 将会是删除的
&emsp;&emsp; (2) 如果 类的某个成员的拷贝构造函数 是删除的或不可访问的(例如是`private`)，则类的合成拷贝构造函数被定义为删除的。如果类的某个成员的析构函数是删除的或不可访问的，则类合成的拷贝构造函数也被定义为删除的。
&emsp;&emsp; (3) 如果类的某个成员的拷贝赋值运算符是删除的或不可访问的，或是类有一个const的或引用成员，则类的合成拷贝赋值运算符被定义为删除的。
&emsp;&emsp; (4) 如果类的某个成员的析构函数是删除的或不可访问的，或是类有一个引用成员，它没有类内初始化器(参见2.6.1节，第65页)，或是类有一个const成员，它没有类内初始化器且其类型未显式定义默认构造函数,则该类的默认构造函数被定义为删除的。

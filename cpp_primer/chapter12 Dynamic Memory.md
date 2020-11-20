# 动态内存

## 一、new 和 delete
### 1.1 可以为new分配的对象命名吗？
&emsp;&emsp;不能，因为在堆上分配的内存是无名的，因此new无法为期分配的对象命名。
### 1.2 new的返回值是什么？
&emsp;&emsp;new返回的是 一个指向动态分配对象的指针：
```cpp
int *pi = new int(3); 
```
### 1.3 使用new动态分配对象时，若没有对其进行初始化将会发生什么？
&emsp;&emsp;默认情况下，动态分配的对象时默认初始化的，这意味着：
>&emsp;① 内置类型 的对象的值将是未定义的；
>&emsp;② 类类型 的对象将调用其默认构造函数进行初始化
```cpp
int *pi = new int;          // int是内置类型，因此pi指向一个未初始化的int
string *ps = new string;    // string是类，将调用其默认构造函数进行初始化，因此ps为空string
```
### 1.4 如何初始化new出来的对象？
&emsp;&emsp; 可以使用 直接初始化、传统的构造方式(用圆括号)、列表初始化（花括号，C++11标准）：
```cpp
int *pi = new int(1024);    // 直接初始化
vector<int> * pvec = new vector<int>{1, 2, 3, 4, 5, 6}; // 列表初始化
```
也可以使用 值初始化（在类型名后面根一对空的圆括号即可）：
```cpp
string *ps2 = new string;       // 默认初始化为空string
string *ps2 = new string();     // 值初始化为空string

int * pi1 = new int;    // 默认初始化，*pi1的值是未定义的
int * pi2 = new int();  // 值初始化为0
```
**注意：**
>① 对于类类型，是否通过圆括号来要求编译器对其进行值初始化没有什么区别，因为不管不怎么样编译器都会调用其默认构造函数来对其进行初始化；
② 但对于内置类型就不一样了，如果 不通过圆括号来要求编译器对其进行值初始，那么它将进行默认初始化，也就是说它的值将是未定义的，但值初始化就不一样了，对象将有着定义良好的值；
③ 同理，对于依赖编译器合成默认构造函数的类，里面的内置成员也将执行默认初始化，即它们的值将是未定义的。
### 1.5 使用auto来new对象时要注意什么？
① 只有括号中仅含单一的初始化器时才能使用auto；
② 将用初始化器里的值来初始化new出来的对象
③ auto和new配合使用时返回的是指针；
```cpp
int obj = 0;
// pi是指向obj类型的指针
// pi指向的对象将用obj的值来初始化
auto p1 = new auto(obj); 

// 使用auto推断new出来的对象时，初始化器中只能有单一的值
auto p2 = new auto{a, b, c};
```
### 1.6 如何动态分配 const对象？
&emsp;通过new分配const对象是应该注意：
&emsp;&emsp; (1) 和其它const对象一样，一个动态分配的const对象必须进行初始化;
&emsp;&emsp; (2) 通过new分配的const对象 返回的是 一个指向const的指针
```cpp
const int * pci = new const int; // 错误，必须进行初始化
const int * pci = new const int(1024);

const string * pcs = new const string; // 正确，string类有默认构造函数，psc将隐式的被初始化为一个空string
```
**注意**：上面的`pcs`变量虽然没有显示初始化，但 string类  定义了默认构造函数，因此将通过默认构造函数进行隐式初始化，所以没有错。
### 1.7 为什么说 new 可能造成机器的内存耗尽？
&emsp;&emsp; new 出来的内存如果不在使用完后及时释放的话，将一直存在，虽然现在很多计算机内存都很大，但是时间长了还是会积少成多，最后耗尽内存。
### 1.8 内存耗尽了的时候 使用new分配内存 会发生什么？
&emsp;&emsp; 内存耗尽了就意味着就没有内存可以分配了，此时若使用new分配内存将抛出一个`bad_alloc`异常，但我们可以改变使用 new 的方式来阻止它抛异常：
```cpp
int * p1 = new int; // 若此时内存已耗尽，则new将抛出 std::bad_alloc 异常
int * p2 = new (nothrow) int; // 若分配失败，则new将返回一个空指针
```
这种形式的new被称为：**定位new(placement new)**，原因在19.1.2节讲。
**注意**：`bad_alloc` 和 `nothrow` 都定义在 头文件new 中。
### 1.9 如何查看是否内存泄露？
(1) 通过工具：
&emsp;&emsp; linux端工具：Valgrind
&emsp;&emsp; windows工具：Visual Leak Detector
(2) 通过日志：
&emsp;&emsp; 将new和delete分别封装为函数，专门用于分配内存和释放内存，并在函数中记录 所分配内存的地址和长度、释放内存的地址和长度，等到停服务的时候看一下能不能匹配上就能定位内存泄露的代码了。
### 1.10 使用delete释放内存时要注意什么？
&emsp;&emsp; (1) delete 接收的是 指针；
&emsp;&emsp; (2) 传给delete的必须是 **指向动态分配的内存的指针** 或 **空指针**；
```cpp 
int i, *pi1 = &i, *pi2 = nullptr;
double *pd = new double(33), *pd2 = pd;
delete i; // 错误: i不是指针
delete pi1; // 未定义: pi1 指向的是一个局部变量
delete pd; // 正确
delete pd2; // 未定义: pd2 指向的内存已经被释放了
delete pi2; // 正确: delete 空指针是没问题的

const int *pci = new const int(1024);
delete pci; // 正确:  const 对象的值不能改变，但是可以被销毁。
```
### 1.11 什么是 空悬指针(dangling pointer)？如何解决？
&emsp;&emsp; 对于指针 p，它指向的是一块动态分配的内存，在这块内存将在被delete后被释放，此时指针p仍然指向了这段被delete的指针，此时指针p就被称为 空悬指针(dangling pointer)。
&emsp;&emsp; 内存delete之后，马上将指向该内存的指针置为 nullptr，但这也只能提供有限的保护，来看下面的代码：
```cpp
int *p(new int(42));
auto q = p;     // p 和 q 指向了相同的内存
delete p;       // p 现在是 空悬指针
p = nullptr;
```
此时虽然p被置为nullptr了，但q依然是一个空悬指针，在实际系统中，查找指向相同内存的所有指针是异常困难的，因此空悬指针很难被解决。
### 1.12 为什么说有一些动态内存除非在程序关闭时由OS回收外，永远不可能被释放？
我们来看下面的代码：
```cpp
// factory 返回了一个指向动态分配对象的指针
Foo* factory(T arg)
{
    // process arg as appropriate
    return new Foo(arg); // 调用者需要负责释放该动态内存
}

void use_factory(T arg)
{
    Foo *p = factory(arg);
    // 使用p但不delete它
}   // p在离开作用域后被释放，但是p指向的动态内存并没有被释放
```
在`use_factory`函数中，p离开了作用域，但是p指向的动态内存并没有被释放，而且由于 p 是唯一指向该动态内存的指针，因此在p被销毁后，它所指向的内存永远不可能被释放，除了在程序关闭时由OS回收。
### 3.13 使用 new 和 delete 管理动态内存有哪些常见的错误？
&emsp;&emsp;(1) 忘记delete，造成内存泄露
&emsp;&emsp;(2) 使用已经释放了的对象，可以通过将释放的内存置空来检测这种错误；
&emsp;&emsp;(3) 同一块内存被 释放多次。在有 两个指针 指向相同的动态内存时，这种错误很容易犯，因为第一次delete时，空间就已经还给了 堆，再delete的话可能会造成堆被破坏。



&emsp;
## 二、智能指针
### 2.1 标准库定义了哪些智能指针？它们分别用于什么场景？
&emsp; `shared_ptr` 和 `unique_ptr`：
&emsp;&emsp; `shared_ptr`允许多个指针指向同一个对象；
&emsp;&emsp; `unique_ptr`则独占所指向的对象。
### 2.2 智能指针是通过什么实现的？
&emsp;&emsp;和`vector`一样，智能指针也是模板实现的。
### 2.3 使用智能指针有什么好处？
&emsp;&emsp;智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针是一个类，当超出了类的实例对象的作用域时，会自动调用对象的析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。
&emsp;&emsp;也就是说，使用智能指针可以简化程序员的工作，避免 内存泄露
### 2.4 智能指针 可以 托管 非动态分配的对象吗？
&emsp;&emsp;默认情况下，不能，因为智能指针默认，只有指向动态分配的对象的指针才能交给 shared_ptr 对象托管。将指向普通局部变量、全局变量的指针交给 shared_ptr 托管，编译时不会有问题，但程序运行时会出错，因为不能析构一个并没有指向动态分配的内存空间的指针。
&emsp;&emsp; 如果想将智能指针绑定到 一个指向非动态分配的对象，需要提供自己的操作来替代delete



&emsp;
## 三、shared_ptr类
### 3.1 如何定义一个指向 string类型 的 shared_ptr?
```cpp
shared_ptr<string>p1 = "Hello wordl";
```
### 3.2 定义一个 shared_ptr 时，若不对齐进行显式初始化，变量的值是？
&emsp;&emsp;此时将执行默认初始化，里面是一个空指针。
### 3.3 shared_ptr支持的操作
#### 3.3.1 和unique_ptr共有的操作
操作 | 解释
---| ---
`shared_ptr<T>sp` |	空智能指针，可以指向类型为T的对象
`unique_ptr<T>up `|	
`p	     `        |  将p当做 判断条件，若p指向了一个对象则返回`true`
`*p	     `        |  解引用
`p->mem	 `        |  等价于(*p).mem
`p.get()	 `        |  返回p中保存的指针，此时应该小心，因为若智能指针释放了其对象，则p指向的对象也同样被释放了
`swap(p,q)`        |	交换p和q中的指针
`p.swap(q)`        |	交换p和q中的指针
#### 3.3.2 shared_ptr独有的操作
操作                 |解释
----------------------| ---
`make_shared<t>(args)`| 返回一个shared_ptr，指向一个动态分配的类型为T的对象，并使用args初始化该对象
`shared_ptr<T>p(q)	 `| p是shared_ptr q的拷贝，此操作增加q中的计数器，q中的指针必须能转换为T*
`p=q	                 `| p和q都是shared_ptr，所保存的指针必须能相互转换。此操作会递减p中的引用计数，递增q中的引用计数。若p中的引用计数变为0，则将其管理的内存释放
`p.use_count()	 `    | 返回与p共享对象的智能指针数量，此操作可能很慢，平时主要用于调试。
`p.unique()	     `    | 若p.use_count()为1，返回true;否则返回false
#### 3.3.3 如何判断 指向string的智能指针p指向的值是否为空 比较安全？
和常规指针一样，取值之前先判空：
```cpp
if(p && p->empty)
    *p = "Hello";
```
### 3.4 make_shared 函数
#### 3.4.1 作用是？
&emsp;&emsp; make_shared 函数用于分配**动态内存**，可以说是最安全的分配和使用动态内存的方法，此函数 在动态内存中分配一个对象 并初始化它，然后返回指向此对象的 shared_ptr
#### 3.4.2 怎么用？
&emsp;&emsp; 
```cpp
shared_ptr<int> p1 = make_shared<int>(42);
shared_ptr<string> p2 = make_shared<string>(10, "9");
shared_ptr<int> p3 = make_shared<int>();    // 请求值初始化，p3指向的int的值为0
```
也可以和`auto`搭配使用：
```cpp
auto p4 = make_shared<vecrot<string>>();
```
#### 3.4.3 原理是？
&emsp; 和顺序容器的`emplace`操作一样，`make_shared`会调用的是其参数来构造给定类型的对象，比如：
&emsp;&emsp;  ① 对于 `make_shared<string>`,传递的参数必须和 string类 的某个构造函数相匹配；
&emsp;&emsp;  ② 对于 `make_shared<int>`,传递的参数必须可以能用来初始化一个int
### 3.5 shared_ptr 的 引用计数 规则是怎样的？
&emsp; 指向相同资源的所有 shared_ptr 共享“引用计数管理区域”，并采用原子操作保证该区域中的引用计数值被互斥地访问。因此每当进行 **拷贝** 或 **赋值** 操作时，每个`shared_ptr` 都会记录有多少个 其它的`shared_ptr` 和它一样指向了相同的对象，我们可以认为每个 `shared_ptr` 都有一个 关联计数器（通常被称为引用计数）：
&emsp;&emsp;(1) 每当我们拷贝一个 `shared_ptr`，该计数器都会递增，也就是说：① 将`shared_ptr`作为函数的参数传递给一个函数（拷贝）、 ② 将`shared_ptr`作为函数的返回值（拷贝）、③ 将`shared_ptr`拷贝给其它`shared_ptr`都会导致它关联的引用计数递增
&emsp;&emsp;(2) 每当我们给`shared_ptr`赋新值、或`shared_ptr`被销毁（比如一个局部`shared_ptr`离开其作用域）会导致关联的引用计数递减
#### 3.5.1 将一个 `shared_ptr`指针q 赋给 另一个`shared_ptr`指针r 会发生什么？
q的引用计数+1，r原来指向的对象 的引用计数-1
```cpp
auto r = make_shared<int>(99);
// 1. q的引用计数+1
// 2. r原来指向的对象 的引用计数-1
// 3. 因为r原来指向的对象只有一个引用者，所以r原来指向的对象将被自动释放。
r = q; 
```
### 3.6 shared_ptr 通过什么控制 管理的对象的 初始化 和 销毁？
初始化：元素的构造函数
销毁：shared_ptr类的 析构函数负责销毁对象，它会递减它所指向的对象的引用计数，如果引用计数变为0，则shared_ptr的析构函数 不但会销毁shared_ptr对象，还会释放 shared_ptr对象 所指向的内存：
```cpp
shared_ptr<Foo> factory(T arg)
{
    return make_shared<Foo>(arg);//返回shared_ptr，指向一个类型为Foo的对象，用类型为T的arg初始化
}

void use_factory_1(T arg)
{
    shared_ptr<Foo> p = factory(arg);// 调用factory初始化p，p所指对象的引用计数+1变成1
    // use_factory_1 执行结束，销毁p，p所指对象的引用计数-1变成0，p指向的内存也被释放
}

shared_ptr<Foo> use_factory_2(T arg)
{
    shared_ptr<Foo> p = factory(arg);// 调用factory初始化p，p所指对象的引用计数+1变成1
    return p;//返回p，p所指对象的引用计数+1变成2
}// use_factory_2 执行结束，销毁p，p所指对象的引用计数-1变成1，p指向的内存并不会被释放
```
注意比较 `use_factory_1` 和 `use_factory_2`：
&emsp; 因为`use_factory_1`中，`shared_ptr`对象p所指向内存  的引用计数为1，因此在函数结束后，`shared_ptr`对象p 被销毁，它指向的内存的引用计数编程了0，因此`shared_ptr`对象p指向的内存也被释放了。
&emsp;而在`use_factory_2`中，它返回了构造的`shared_ptr`对象，因此引用计数为2，即使函数结束后 `shared_ptr`对象p被销毁，它指向的内存也不会被释放。
### 3.7 使用 `shared_ptr` 就一定不会内存泄露了吗？
&emsp;&emsp;不是的，因为在最后一个指向它的`shared_ptr`被销毁前内存都不会释放，所以还是有可能会造成内存泄露的。
### 3.8 程序一般出于什么原因一定要使用容易造成 内存泄露的 动态内存？
(1) 程序不知道自己需要使用多少对象；
(2) 程序不知道所需对象的准确类型；
(3) 程序需要在多个对象间共享数据
### 3.9 定义和改变shared_ptr的其它方法
```cpp
shared_ptr<T>p(q)       // p管理内置指针q指向的对象，其中q必须指向new分配的内存，而且必须可以转换为T*类型
shared_ptr<T>p(u)       // 从 unique_ptr指针u 那里接管对象的所有权，然后将u置空
shared_ptr<T>p(q, d)    // p接管了 内置指针q 所指向的对象，q必须能转换为T*，并用d来替换delete释放资源

shared_ptr<T>p(p2)      // p是 shared_ptr对象p2的拷贝
shared_ptr<T>p(p2, d)   // 和上面一样，p是 shared_ptr对象p2的拷贝，但是会使用d来替换delete释放资源
```
### 3.10 使用new出来的指针初始化 shared_ptr时要注意什么？为什么？
&emsp;&emsp; **使用new初始化shared_ptr时**必须使用直接初始化，因为 `shared_ptr`类 中接收 指针参数 的构造函数是 `explicit` 的，因此我们不能使用 内置指针(new出来的指针) 隐式转换为一个智能指针：
```cpp
shared_ptr<int> p1 = new int(1024);     // 错误：不能用拷贝初始化，必须使用 直接初始化
shared_ptr<int> p2(new int(1024));      // 正确: 直接初始化
```
这个错误在返回`shared_ptr`指针时很容易犯：
```cpp
shared_ptr<int> clone(int p){
    return new int(p); // 错误：映射转换为 shared_ptr
}
```
应该怎么写才对：
```cpp
shared_ptr<int> clone(int p){
    return shared_ptr<int>(new int(p)); // 正确：显示的创建了一个 shared_ptr
}
```
**注意：** 并不是`shared_ptr`不能进行 拷贝初始化，而是不能通过 内置指针 对 `shared_ptr` 进行拷贝初始化！
### 3.11 混用 普通指针 和 智能指针有何风险？
来看下面这个函数：
```cpp
// ptr 在函数被调用时 被创建并初始化
void process(shared_ptr<int> ptr){
    // 使用 ptr
} // ptr 离开作用域后被销毁
```
然后考虑下面的调用：
```cpp
int *x(new int(1024)); //  x 是一个 指向动态内存的内置指针
process(x); // 错误1: 无法将 int* 转换为 shared_ptr<int>
process(shared_ptr<int>(x)); // 正确, 用内置指针来初始化 实参
int j = *x; // 错误2：未定义的行为: x 在调用完process后就被释放了
```
上面的代码显然是错误的：
&emsp;&emsp;**错误1**：使用new出来的指针初始化shared_ptr时只能直接初始化，而直接传 x 进去，是用x对 process的形参ptr 进行拷贝初始化，这样显然是错误的；
&emsp;&emsp;**错误2**：因为我们将 一个临时的shared_ptr(即`shared_ptr<int>(x)`) 传给了 process函数，内置指针x 将对 rocess的形参ptr 进行直接初始化，这是合法的，但在process函数结束后，实参ptr就被销毁了，因为ptr和x指向的是同一动态内存，因此后面对 指针x访问显然是错误的。
正确的调用应该为：
```cpp
shared_ptr<int> p(new int(42)); // 此时只能指针p的引用计数为1
process(p); // 1. 通过p对形参ptr进行拷贝初始化，因此在函数里面时，p的引用计数为 2
            // 2. 函数调用结束后，参数ptr被销毁，此时p的引用计数为1

int i = *p; // 正确: p的引用计数为1
```
**总结：**使用一个内置指针来访问一个智能指针所负责的对象是很危险的，因为我们无法知道对象何时被销毁。
### 3.12 为什么推荐make_shared函数，而不是new运算符？
&emsp;&emsp; 智能指针`shared_ptr`可以协调对象的析构，但这仅限于其自身的拷贝(指的是`shared_ptr`对象之间的拷贝)，使用 `make_shared`函数可以在 分配对象的同时 就将 `shared_ptr`与其绑定，从而避免了无意间将同一块内存绑定到了多个独立的`shared_ptr`上
### 3.13 `shared_ptr`的 get函数
#### 3.13.1 get函数的作用？
&emsp;&emsp;智能指针定义了一个`get`函数，它返回一个内置指针，指向智能指针管理的对象。
#### 3.13.2 为什么要定义 get函数？
&emsp;&emsp;这个函数时为了这样一种情况而设计的：我们需要向不能使用智能指针的代码传递一个内置指针
#### 3.13.3 使用建议
不&emsp;&emsp;要用get初始化另一个智能指针或为智能指针赋值，我们来看下面的代码：
```cpp
shared_ptr<int> p(new int(42)); // 智能指针p 的引用计数为 1
int *q = p.get();               // 正确: 但要保证 q不会释放它指向的内存
{   // 新的程序块
    // 未定义: 两个独立的 shared_ptrs 指向了相同的内存
    shared_ptr<int>r(q);
} // 程序块结束, r被销毁，且它指向的内存也被delete
int foo = *p; // undefined; the memory to which p points was f
```
在上面的代码中，当我们使用p时会发生未定义的行为，而且当p销毁时，这块内存会被第二次delete
### 3.14 `shared_ptr`的 reset函数
reset()函数有如下几个版本：
```cpp
p.reset()       // 若p是唯一指向其对象的 shared_ptr，则reset会释放该对象
p.reset(q)      // 令 p 指向 内置指针q
p.reset(q,d)    // 令 p 指向 内置指针q，使用d来释放q，而不是delete
```
**注意事项：**
不能用get出来的指针reset另一个智能指针，道理和前面一样。




https://zhuanlan.zhihu.com/p/63488452
https://www.zhihu.com/question/61008381
https://www.zhihu.com/question/319277442
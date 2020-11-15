# 动态内存

## 1. new 和 delete
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
### 使用delete释放内存时要注意什么？
&emsp;&emsp; (1) delete 接收的是 指针；
&emsp;&emsp; (2) 传给delete的必须是 指向动态分配的内存 或 一个空指针；
### 什么是 空悬指针(dangling pointer)？如何解决？
&emsp;&emsp; 对于指针 p，它指向的是一块动态分配的内存，在这块内存将在被delete后被释放，此时指针p仍然指向了这段被delete的指针，此时指针p就被称为 空悬指针(dangling pointer)。
&emsp;&emsp; 内存delete之后，马上将指向该内存的指针置为 nullptr，但这也只能提供有限的保护，来看下面的代码：
```cpp
int *p(new int(42));
auto q = p;// p 和 q 指向了相同的内存
delete p；
p = nullptr；
```
此时虽然p被置为nullptr了，但q依然是一个空悬指针，在实际系统中，查找指向相同内存的所有指针是异常困难的，因此空悬指针很难被解决。



&emsp;
## 2. 智能指针
### 2.1 标准库定义了哪些智能指针？它们分别用于什么场景？
&emsp; `shared_ptr` 和 `unique_ptr`：
&emsp;&emsp; `shared_ptr`允许多个指针指向同一个对象；
&emsp;&emsp; `unique_ptr`则独占所指向的对象。
### 2.2 智能指针是通过什么实现的？
&emsp;&emsp;和`vector`一样，智能指针也是模板实现的。
### 2.3 使用智能指针有什么好处？
&emsp;&emsp;智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针是一个类，当超出了类的实例对象的作用域时，会自动调用对象的析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。
&emsp;&emsp;也就是说，使用智能指针可以简化程序员的工作，避免 内存泄露
### 2.4 



&emsp;
## 3. shared_ptr类



.TODO:
https://zhuanlan.zhihu.com/p/63488452
https://www.zhihu.com/question/61008381
https://www.zhihu.com/question/319277442
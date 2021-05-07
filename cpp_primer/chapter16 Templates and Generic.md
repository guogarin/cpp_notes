
# 第十六章 模板与泛型编程

## 1 面向对象编程(OOP) 和 泛型编程(GP) 有何异同？
&emsp;它们都能在处理编写程序时不知道类型的情况。不同之处在于：
* &emsp; OOP能处理 `运行时` 获取类型的情况
* &emsp; GP能处理 `编译期` 可获取类型的情况






&emsp;
&emsp;
## 2 泛型编程的基础是什么？
&emsp;&emsp; 模板 是C++泛型编程的基础。






&emsp;
&emsp;
## 3 什么是模板？
&emsp;&emsp; 模板 就是一个 创建类或函数 的蓝图或者说公式。当使用一个`vector`这样的泛型类型，或者说`find()`这样的泛型函数时，我们提供足够的信息，将蓝图转换为特定的类或函数，**这种转换发生在编译时**。






&emsp;
&emsp;
## 4 泛型编程有哪些应用？
&emsp;&emsp; 标准库的容器、迭代器、算法都是泛型编程应用的例子。






&emsp;
&emsp;
## 5 简单阐述一下模板可以替我们做什么？
&emsp;&emsp; 就拿最简单的数值比较来说吧，我们需要编写一个函数来比较两个数值的大小，函数指出第一个值是小于、等于还是大于第二个数，然而在实际应用中，我们需要定义多个重载的函数，每个函数比较一个特定的类型：
```cpp
// returns 0 if the values are equal, -1 if v1 is smaller, 1 if v2 is smaller
int compare(const string &v1, const string &v2)
{
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}

// 重载
int compare(const double &v1, const double &v2)
{
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}
```
如果需要比较其它类型的话，我们继续对`compare()`进行重载，这样代码就会显得很多，但是这些函数的内部逻辑其实都是一样，我们有没有其它方法来简化代码呢？这个时候 函数模板就派上用场了：






&emsp;
&emsp;
## 6 函数模板(function template)
### 6.1 如何理解函数模板？
&emsp;&emsp; 一个函数模板就是一个公式，可用来生成针对特定类型的函数版本。

### 6.2 如何编写函数模板？
&emsp;&emsp; 模板的定义一关键字`template`开始，后面跟着一个**模板参数列表(template parameter list)**，它是一个 逗号分隔的 一个或多个 模板参数(template parameter)的列表，用`<`和`>`包围起来，**值得注意的是**，模板参数列表不能为空！
&emsp;&emsp; 
对于前面的两个`compare()`，我们可以为其编写函数模板如下：
```cpp
template<typename T>
int compare(const T &v1, const T &v2)
{
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}
```

### 6.3 如何使用函数模板？
像使用函数那样使用即可：
```cpp
cout << compare(1, 0) << endl; // 此时T为int
```

### 6.4 调用函数模板时，如何确定`T`的类型呢？
&emsp;&emsp; 当我们调用一个函数模板时，编译器（通常）用函数实参来推断模板实参，例如，我们在调用`compare`函数模板的时候，编译器将使用实参的类型来推断绑定到模板参数`T`的类型。

### 6.5 对于如下的调用，编译器生成什么样的代码？
```cpp
cout << compare(1, 0) << endl; // T is int

vector<int> vec1{1, 2, 3}, vec2{4, 5, 6};
cout << compare(vec1, vec2) << endl; // T is vector<int>
```
编译器根据实参推断出上述调用`T`分别为：`int`和`vector<int>`生成对应的代码如下：
```cpp
// compare(1, 0) 
int compare(const int &v1, const int &v2)
{
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}

// compare(vec1, vec2)
int compare(const vector<int> &v1, const vector<int> &v2)
{
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}
```
上述这些编译器生成的版本通常被称为 模板的实例(instantiation)

### 6.6 模板的 类型参数(type parameter)
#### 6.6.1 什么是模板的 类型参数？
```cpp
template<typename T>
int compare(const T &v1, const T &v2)
{
    // 略...
}
```
在上面的代码中，`T`就是 类型参数
#### 6.6.2 使用类型参数时需要注意什么？
类型实参前必须使用关键字`typename`或`class`
```cpp
// 错误，U前面没有关键字typename或class
template <typename T, U> calc(const T&, const U&);
```
#### 6.6.2 在模板参数列表中，`typename`或`class`有何区别？
&emsp;&emsp; 没有区别。可以互换使用，一个模板参数列表中可以同时使用这两个关键字。
&emsp;&emsp; 看起来`typename`要比`class`直观许多，毕竟我们可以使用内置(非类类型)来作为模板类型实参，而且`typename`更清楚的指出随后的名字是一个类型名。可以在模板参数列表中使用`class`关键字 是为了兼容以前的C++版本，因为`typename`是在模板已经广泛使用之后才引入C++的，以前都是使用`class`关键字的。
#### 6.6.3 在模板参数列表中，只能使用`typename`或`class`吗？
&emsp;&emsp; 不是，还能使用 非类型模板参数(nontype parameter)，但是要注意的是，一个 非类型参数 表示一个值 而不是表示一个类型！
#### 6.6.4 如何理解下面的代码？
```cpp
template<unsigned N, unsigned M>
int compare(const char (&p1)[N], const char (&p2)[M])
{
    return strcmp(p1, p2);
}

int result = compare("hi", "mom");
```
这是一个函数模板，`compare`的两个形参都是数组的引用(详见《C++ primer》6.2.4节)，其中`N`和`M`都是非类型参数，分别表示数组`p1`和`p2`的长度，这是编译器根据传入的实参推断得到的。其中`int result = compare("hi", "mom");`将会被实例化出如下版本：
```cpp
int compare(const char (&p1)[3], const char (&p2)[4])
{
    return strcmp(p1, p2);
}
```
#### 6.6.5 什么样的值 可以作为 非类型参数 的实参？
&emsp;&emsp; 类型类模板参数的模板实参必须是 常量表达式。

### 6.7 如何编写类型无关的模板函数？
应该尽量减少对实参类型的要求，比如：
(1) 形参应该为 引用，这样就能确保函数可以用于 不可靠拷贝的类型；
(2) 用`less`来替代`<`进行比较操作；






&emsp;
&emsp;
## 7 模板的编译
### 7.1 模板在什么时候生成代码？
&emsp;&emsp; 当编译器遇到一个模板定义时，它并不生成代码，而是在我们实例化模板时，编译器才会生成代码。

### 7.2 模板函数（模板类）的声明和定义可以分开吗？也就是把声明放在头文件中，定义放在源文件
&emsp;&emsp; 当我们调用一个函数时，编译器只需要掌握函数的声明，类似的，当我们使用一个类类型的对象时，类型一必须是可用的，但成员函数的定义不必已出现，因此我们可以把函数声明和类定义放在头文件中，而将普通函数的定义和类成员函数的定义放在源文件中。
&emsp;&emsp; 而模板则不同，为了生成一个实例化版本，编译器需要掌握函数模板或类模板成员函数的定义。因此，和 非类模板代码不一样的是，模板的头文件通常 既包括声明也包括定义。

### 7.3 模板代码一般在什么时候报错？ 为什么呢？
&emsp;&emsp; 因为模板直到实例化时才会生成代码，因此 模板的报错时间 和 非模板代码 有所不同。通常编译器会在三个阶段对模板进行报错：
(1) 第一阶段：编译模板本身时。
&emsp;&emsp; 这个时候一般检查语法错误。
(2) 第二阶段：编译器遇到模板使用时。
&emsp;&emsp; 此时对于函数模板的调用，编译器会检查实参数目是否正确、参数类型是否匹配。对于类模板，会检查模板实参数量是否正确。
(3) 第三阶段：模板实例化时
&emsp;&emsp; 由模板生成代码，检查其中类型相关的错误。这类错误可能在链接期才会被发现






&emsp;
&emsp;
## 8 类模板(class template)
&emsp;&emsp; 人们需要编写多个形式和功能都相似的函数，因此有了函数模板来减少重复劳动；人们也需要编写多个形式和功能都相似的类，于是 C++ 引人了类模板的概念，编译器从类模板可以自动生成多个类，避免了程序员的重复劳动。
### 8.1 类模板 和 函数模板 在使用上有何不一样？
&emsp;&emsp; 和函数模板不一样的是，**编译器 不能为类模板 推断模板参数类型**。因此在使用类模板时，我们必须在模板名后用尖括号`<>`提供实参：
```cpp
vector<int> vec{2, 3}; // vector是类模板，我们需要提供参数的类型
compare(2, 3);
```

### 8.2 如何定义一个模板类？
&emsp;&emsp; 值得注意的是，模板类的声明和定义应该在同一个头文件中（原因见上文的笔记）。
#### 8.2.1 定义类模板
&emsp;&emsp; 类似于函数模板，类模板以关键字`template`开始，后跟模板参数列表。在类模板及其成员函数定义中，使用模板参数代替使用模板时用户提供的类型或值
```cpp
template <typename T/*模板形参表*/>
class T_Name/*类模板名*/{
　　// 成员列表
};
```
#### 8.2.2 类模板的成员函数
&emsp;&emsp; 和其它类一样，我们既可以在类的内部定义成员函数，也可在外部定义。而且在内部定义的成员函数将被隐式声明为内联函数。
&emsp;&emsp; 类模板的成员函数本身是一个普通函数，但是类模板的每个实例都有其自己版本的成员函数，故类模板的成员函数 具有和 类模板 相同的模板参数。因此 **定义在类模板外的成员函数** 需要以`template`开始，后接与类模板相同的模板参数列表:
```cpp
template <typename T>
ret-type/*返回类型*/ T_Name<T>::member-name/*成员函数名*/(parm-list/*成员函数的形参列表*/)
{
    // 函数体
}
```

### 8.3 用同一个类模板实例化出来的类有关联吗？
&emsp;&emsp; 没有，因为类模板的每个实例都是独立的类。使用不同模板实参实例化出的类之间没有关联，也没有特殊的访问权限。

### 8.4 实例化一个类模板后，该类模板的 所有成员函数 都会立马进行实例化吗？
&emsp;&emsp; 不是的，默认情况下，对于一个实例化了的类模板，其成员函数只有在使用时才会被实例化，那些没有使用到的成员函数不会被初始化。换句话说，假设一个模板类有10个成员函数，但是实例化后只用到了3个，那只有使用到的这三个会被实例化，剩下的6个不会被实例化。
```cpp
Blob<int> squares = {0,1,2,3,4,5,6,7,8,9};
// instantiates Blob<int>::size() const
for (size_t i = 0; i != squares.size(); ++i)
squares[i] = i*i; // instantiates Blob<int>::operator[](size_t)
```
对于上面的代码，只有`operator[]`、`size()`和 `接受initializer_list<int>的构造函数`会被实例化，`Blob<int>`的其它成员函数不会被实例化。

### 8.5 在使用类模板时，什么时候可以省略模板参数？
### 8.5.1 何时可以省略？
&emsp;&emsp; **使用一个类模板时必须提供模板实参，有一个例外**：在类模板自己的作用域中，可直接使用模板名而不提供实参。
```cpp
// BlobPtr throws an exception on attempts to access a nonexistent element
template <typename T> class BlobPtr{
public:
    // 其它公有成员，略...
    // 注意，这里的 ++ 和 -- 运算符 的返回值 都没有加 类型T
    BlobPtr& operator++(); 
    BlobPtr& operator--();
private:
    // 私有成员，略...
};
```
&emsp;&emsp; 处于类模板的作用域中时，编译器处理模板自身引用时，就像已经提供了与模板参数相同的实参一样。
对于`BlobPtr<T>::operator++`和`BlobPtr<T>::operator--`，其类似于下面的代码：
```cpp
BlobPtr<T>& operator++(); // 提供了类型T
BlobPtr<T>& operator--();
```
### 8.5.2 在类外定义的时候也可以省略模板参数吗？
&emsp;&emsp; 不可以。因为在类模板外定义成员时，**直到遇到类名才进入类作用域**。即，返回类型中出现模板自身时需要提供模板实参。若不提供模板实参，则编译器假定使用的实参与成员实例化所用的实参一致
```cpp
template <typename T>
BlobPtr<T>/*① 注意，类外定义成员函数时，不能省略模板参数！*/ BlobPtr<T>::operator++(int)
{
    BlobPtr ret = *this; // ② 此时已经在类的作用域内了，可以省略 参数类型T
    ++*this; 
    return ret; 
}
```
对于 类外定义的`BlobPtr<T>::operator++`，因为返回类型已经在类的作用域之外了，因此我们必须指出返回类型是一个实例化的`BlobPtr`，而在函数体类，我们已经进入类的作用域，因此`BlobPtr ret = *this;`无需写成`BlobPtr<T> ret = *this;`。
### 8.5.3 总结
&emsp;&emsp; 能不能省略模板参数，**关键是看 是否在类作用域内**。在类作用域内可以省略模板参数，在类作用域外不能省略。那问题来了，怎么确定是否在类作用域内呢？
① 在类的定义的花括号体内时，我们处于类的作用域内；
```cpp
template <typename T>
class T_Class{
    // 作用域内
}
```
② 在类外定义成员函数时，类名 到 函数体结束这段区间内，我们处于类作用域内
```cpp
template <typename T>
BlobPtr<T>/*① 在此之前，都不在类作用域内*/ BlobPtr<T>::operator++(int)
{
    // ② 此时已经在类的作用域内了，可以省略 参数类型T
    // 函数体，略...
}
```

### 8.6 类模板 和 友元
&emsp;若一个类模板包含一个友元时：
&emsp;&emsp; 若该友元**不是**模板，则友元是该类模板所有实例的友元
&emsp;&emsp; 若该友元**是**模板，则该类模板可以授权给所有友元模板实例，也可只授权给特定实例。
#### 8.6.1 一对一 的友元关系
&emsp;&emsp; 类模板与另一个模板间友好关系的最常见形式是：只授权给模板实参相同的友元模板：
```cpp
// 前向声明
template <typename> class BlobPtr; 
template <typename> class Blob;     // 下面的 operator== 要用到 Blob，因此需要前向声明

template <typename T>
    bool operator==(const Blob<T>&, const Blob<T>&);

template <typename T> class Blob {
    // 每个Blob将访问权限授予 用相同类型实例化的 BlobPtr 和 ==运算符
    friend class BlobPtr<T>;
    friend bool operator==<T>
        (const Blob<T>&, const Blob<T>&);
    // other members as in § 12.1.1 (p. 456)
};
```
在上述代码中，友元的声明用`Blob`的模板形参作为它们自己的模板实参。因此，友好关系被限定在用相同类型实例化的`Blob`、`BlobPtr`、相等运算符之间：
```cpp
Blob<char> ca; // BlobPtr<char> 和 operator==<char> 都是本对象的友元
Blob<int> ia; // BlobPtr<int> 和 operator==<int> 都是本对象的友元
```
#### 8.6.2 如何将 另一个模板的所有实例都声明为自己的友元？ 如何限定特定实例为自己的友元？
##### (1) 将一个模板类的所有实例 声明为 另一个类模板的所有实例的友元：
```cpp
template <typename T> class C2 { 
    template <typename X> friend class Pal2;
};
```
我们可以看到 `类模板C2`用的模板参数是`T`，而`类模板Pal2`用的模板参数是`X`
##### (2) 将一个模板类的 特定实例 声明为 另一个类模板的所有实例的友元：
```cpp
template <typename T> class Pal; // 前向声明，在将模板的一个特定实例声明为友元时要用到

class C { // C 是一个普通的非模板类
    friend class Pal<C>; // 用 类C 实例化的Pal 是C的一个友元
};
```
我们可以看到，只需给 `类模板Pal`提供 参数类型(如上文中的`类类型C`)，就能做到。
##### (3) 总结
```cpp
template <typename T> class Pal;// 前向声明，在将模板的一个特定实例声明为友元时要用到

class C { // C 是一个普通的非模板类
    friend class Pal<C>; // 用 类C 实例化的Pal 是C的一个友元
    
    template <typename T> friend class Pal2;// Pal2 的所有实例都是C的友元，这种情况无序前置声明！
};

template <typename T> class C2 { // C2 是一个 类模板
    // C2 的每个实例将相同实例化的Pal声明为友元
    friend class Pal<T>; // Pal 的模板声明必须在作用域之内
    
    template <typename X> friend class Pal2;// Pal2 的所有实例都是C2的每个实例的友元
    
    friend class Pal3; // Pal3 是一个非模板类，它是C2所有实例的友元。Pal3 无需前置声明
};
```
#### 8.6.3 如何将 模板自己的类型参数 成员友元？这有啥用？
在C++11中，我们可以将模板参数类型声明为友元：
```cpp
template <typename Type> class Bar {
    friend Type; // grants access to the type used to instantiate Bar
    // ...
};
```
在上面的代码中，我们将用来实例化`类模板Bar`的 类型`Type` 声明为其友元。举个例子：
> 对于`Bar<Sales_data>`，`Sales_data类`将成为 实例`Bar<Sales_data>` 的友元
> 
值得注意的是，通常来说友元应该是一个 函数 或 类，但我们完全可以用一个 内置类型来实例化`Bar`，而且这种与内置类型为友元的情况是被允许的。

### 8.7 类模板的 类型别名
#### 8.7.1 `typedef pair<T, T> twin` 对吗？ 为什么？ 如果错了，应该怎么修改？
**对不对？ 为什么**
&emsp;&emsp; 不对，因为模板不是类型，故不可定义typedef来引用模板。
**怎么改？**
&emsp;&emsp; 
```cpp
template<typename T> using twin = pair<T, T>;
```
#### 8.7.2 为类模板 定义 类型别名 有何用处？
**(1) 方便**
```cpp
template<typename T> using twin = pair<T, T>;
twin<string> authors;   // authors 是一个 pair<string, string>
twin<int> win_loss;     // win_loss 是一个 pair<int, int>
twin<double> area;      // area 是一个 pair<double, double>
```
**(2) 就单纯的取个别名**
#### 8.7.3 为一个 类模板取别名时，可以固定一个或多个模板参数吗？
可以
```cpp
template <typename T> using partNo = pair<T, unsigned>;
partNo<string> books; // books is a pair<string, unsigned>
partNo<Vehicle> cars; // cars is a pair<Vehicle, unsigned>
partNo<Student> kids; // kids is a pair<Student, unsigned>
```
#### 8.7.4 总结
&emsp;&emsp; 因为 类模板 的类型不确定，因此我们不能用`typedef`来为其取别名，而是要通过`using声明`来为其取别名。

### 8.8 类模板的 `static成员`
#### 8.8.1 类模板的 `static成员` 和普通类的`static成员`有何不一样？
&emsp;&emsp; 在下面这段代码中，`Foo`是一个模板类，它有一个名为 `count` 的 public static 成员函数 和一个名为 `ctr` 的 private static 数据成员:
```cpp
template <typename T> class Foo {
public:
    static std::size_t count() { return ctr; }
    // other interface members
private:
    static std::size_t ctr;
    // other implementation members
};
```
每个 `Foo` 的实例都有自己的 static成员实例。即，对任意给定类型`X`，都有一个`Foo<X>::ctr` 和一个 `Foo<X>::count()成员函数`。所有 `Foo<X>`类型的对象共享相同的 `ctr对象`和 `count函数`。例如：
```cpp
// 实例化 static 成员 Foo<string>::ctr 和 Foo<string>::count
Foo<string> fs;
// /所有三个对象共享相同的静态成员，即 Foo<int>::ctr 和 Foo<int>::count 成员
Foo<int> fi, fi2, fi3;
```
**总结：**
&emsp;&emsp; 在 普通的类 和 类模板 中，静态成员其实有着一样的特性：无论新建多少个对象，同一个静态成员都只有一个。
&emsp;&emsp; 对于类模板（就拿类模板`Foo`来举例），编译器会将 `Foo<string>` 和 `Foo<int>` 实例化成两个不同的类，这些被实例化的类 其实和 普通的类 是一样的，可以用来构建类对象。对于`Foo<string>`，无论你用它新建多少个对象，都只存在 一个`Foo<string>::ctr` 和 一个`Foo<int>::count`，因为它俩都是静态成员。

### 8.9 模板参数 的作用域
&emsp;&emsp; 模板参数遵循普通的作用域规则，**一个模板参数名的可用范围** 是在其声明之后 至 模板声明或定义结束之前：
```cpp
typedef double A;

template <typename A, typename B> void f(A a, B b)
{
    // 外部的 A 被隐藏了

    A tmp = a; // tmp的类型 为 模板参数A，而不是 double
    double B;  // 错误: 重复声明 模板参数B
}
```

### 8.10 模板声明
&emsp;&emsp; 
(1) 模板声明中 **必须包含 模板参数**
(2) 和函数参数一样，声明中的 模板参数的名字 不必和定义中相同
```cpp
// 声明 但不定义 compare 和 Blob
template <typename T> int compare(const T&, const T&);
template <typename T> class Blob;

// 注意！下面 3个calc 都指向 相同的 函数模板
template <typename T> T calc(const T&, const T&); // declaration
template <typename U> U calc(const U&, const U&); // declaration
// 定义
template <typename Type>
Type calc(const Type& a, const Type& b) 
{
    /* 略 */ 
}
```
### 8.11 使用类的类型成员












```cpp

```

## 重写`strBlob`类
```cpp
template <typename T> class Blob {
public:
    typedef T value_type;
    typedef typename std::vector<T>::size_type size_type;
    // constructors
    Blob();
    Blob(std::initializer_list<T> il);
    // number of elements in the Blob
    size_type size() const { return data->size(); }
    bool empty() const { return data->empty(); }
    // add and remove elements
    void push_back(const T &t) {data->push_back(t);}
    // move version; see § 13.6.3 (p. 548)
    void push_back(T &&t) { data->push_back(std::move(t)); }
    void pop_back();
    // element access
    T& back();
    T& operator[](size_type i); // defined in § 14.5 (p. 566)
private:
    std::shared_ptr<std::vector<T>> data;
    // throws msg if data[i] isn't valid
    void check(size_type i, const std::string &msg) const;
};
```
### 如何理解 `typedef T value_type;` ?
https://blog.csdn.net/LG1259156776/article/details/77992822?utm_source=blogxgwz13

### 如何理解 `typedef typename std::vector<T>::size_type size_type;` ？
```cpp

```

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
```cpp
// version of compare that will be correct even if used on pointers; see § 14.8.2 (p.575)
template <typename T> int compare(const T &v1, const T &v2)
{
    if (less<T>()(v1, v2)) return -1;
    if (less<T>()(v2, v1)) return 1;
    return 0;
}
```






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

### 8.11 在类模板中 使用 类的类型成员
#### 8.11.1 在类模板中使用类的类型成员 和 在非模板的类使用有何区别？
&emsp;&emsp; 回忆一下，我们用作用域运算符(::)来访问 `static成员` 和 类型成员。在普通（非模板）代码中，编译器掌握类的定义，因此它知道通过作用域运算符访问的名字是类型还是`static成员`。例如，如果我们写下`string::size_type;`编译器有`string`的定义，从而知道`size_type`是一个类型。
&emsp;&emsp; 但对于模版代码就存在困难。例如，假定T是一个模板参数，当编译器遇到类似`T::mem`这样的代码时，它不会知道mem是一个类型成员还是一个`static成员`，直到实例化时才知道。但是为了处理模板，编译器必须知道名字是否表示一个类型。例如，假定T是一个类型参数的名字，当编译器遇到如下形式的语句时：
```cpp
T::size_type * p;// 这是在 定义一个指针 还是在 一个乘法运算 呢？
```
它需要知道我们是正在定义一个名为p的变量还是将一个名为`size_type`的`static数据成员`与名为`p`的变量相乘。
#### 8.11.2 如何 在类模板中 使用 类的类型成员？
&emsp;&emsp; 默认情况下，C++语言假定通过作用域运算符访问的名字不是类型。因此，如果我们希望使用一个模板类型参数的类型成员，就必须显示告诉编译器该名字是一个类型。我们通过使用关键字`typename`来实现这一点：
```cpp
template <typename T>
typename T::value_type top(const T& c)
{
    if(!c.empty())
        return c.back();
    else
        return typename T::value_type();
}
```
#### 8.11.3 有没有什么简便(偷懒)的方法 可以在类模板中 使用 类的类型成员？
使用`typedef`：
```cpp
template <typename T> class Blob {
public:
    typedef T value_type;
    typedef typename std::vector<T>::size_type size_type;
    // 略...
private:
    // 略...
};
```
**详解：**
① `typedef typename std::vector<T>::size_type size_type;`
&emsp;&emsp; `size_type` 会是 `typename std::vector<T>::size_type` 的别名
② ``typename std::vector<T>::size_type``
&emsp;&emsp; 这告诉编译器 `std::vector<T>::size_type`是一个类型，因为在默认情况下，C++语言假定通过作用域运算符访问的名字不是类型。
③ 总结
&emsp;&emsp; `typedef`创建了存在类型的别名，而`typename`告诉编译器`std::vector<T>::size_type`是一个类型而不是一个成员。

### 8.12 默认模板实参（default template argument）
#### 8.12.1 如何使用 模板实参？
&emsp;&emsp; 在新标准中，我们可以为 模板函数、类模板提供实参。
```cpp
// compare has a default template argument, less<T>
// and a default function argument, F()
template <typename T, typename F = less<T>> // F的默认实参是 less<T>
int compare(const T &v1, const T &v2, F f = F())
{
    if (f(v1, v2)) return -1;
    if (f(v2, v1)) return 1;
    return 0;
}
```
**语法解释：**
(1) `typename F = less<T>`
&emsp;&emsp; 定义了一个模板参数`F`，它表示可调用对象(详见第14章的笔记)
(2) `F f = F()`
&emsp;&emsp; 定义了一个新的函数参数`f`，它绑定到一个可调用对象上

#### 8.12.2 使用默认模板实参时需要注意什么？
&emsp;&emsp; 和函数默认实参一样，对于一个模板参数，只有当它右侧的所有参数都有默认实参时，它才可以有默认实参。
```cpp
template <typename T1=int, typename T2 > // 错误，T2没有默认实参
int func(const T1 &v1, const T2 &v2)
{
    // 略...
```
#### 8.12.3 如何在类模板中使用默认实参？
&emsp;&emsp; 
```cpp
template <class T = int> class Numbers { // by default T is int
public:
    Numbers(T v = 0): val(v) { }
    // various operations on numbers
private:
    T val;
};
```
值得注意的是，**即使使类模板有默认参数，在使用时你也要带上尖括号：**
```cpp
Numbers<long double> lots_of_precision;
Numbers<> average_precision; // empty <> says we want the default type
```






&emsp;
&emsp;
## 9 成员模板（member template）
### 9.1 什么是成员模板？
&emsp;&emsp; 一个类（无论是 普通类 还是 类模板）可以包含本身时模板的成员函数，这种成员被称为**成员模板**

### 9.2 编写 成员模板 时需要注意什么？为什么？
成员模板函数不能为虚函数，同时也不能有默认参数。
**(1) 为什么不能是虚函数？**
&emsp;&emsp; 编译器在编译一个类的时候，需要确定这个类的虚函数表的大小。一般来说，如果一个类有`N`个虚函数，它的虚函数表的大小就是`N`，如果按字节算的话那么就是`8*N`(因为64bit的机器上，指针未8字节)。 
&emsp;&emsp; 如果允许一个成员模板函数为虚函数的话，因为我们可以为该成员模板函数实例化出很多不同的版本，也就是可以实例化出很多不同版本的虚函数，那么编译器为了确定类的虚函数表的大小，就必须要知道我们一共为该成员模板函数实例化了多少个不同版本的虚函数。显然编译器需要查找所有的代码文件，才能够知道到底有几个虚函数，这对于多文件的项目来说，代价是非常高的，所以才规定成员模板函数不能够为虚函数。 
我们来看下面的例子（下面的代码是错误的，只是为了说明为什么成员模板为什么不能是虚函数）：
```cpp
class Calc 
{ 
public: 
    template virtual T Add(const T& lhs,const T& rhs) { 
        T sum = lhs + rhs; 
        return sum; 
    } 
}; 

void main() 
{ 
    Calc ins; 
    int nResult = ins.Add(1,2); 
    double fResult = ins.Add(1.0,2.0); 
}
```
对于上面的代码，如果编译器允许模板可以是虚函数，那么编译器需要查看main的代码，才能够知道类Calc一共有两个虚函数，一个是`virtual int Add(const int&,const int&)`，另一个是v`irtual double Add(const double&,const double&)`。 因为只有这样才能够确定该类的虚函数表的大小为2，虽然可行，但是费事，代价过高。 
**(2) 为什么不能带有默认参数？**
需要注意的是，这里所说的默认参数有两种，必须分清楚：
> 第一种是 函数的默认参数
> 第二种是 函数模板的默认模板参数
> 
成员模板函数和普通函数一样，可以有函数的默认参数（第一种），但是不可以有函数模板的默认模板参数（第二种）。如： 
```cpp
//第一种的情况： 
template <typename T>
T sum(T* b,T* e,T result = T()) //这里的result就是函数的默认参数，允许 
{ 
    while(b!=e) 
    result += *b++; 
    return result; 
}; 
//第二种的情况： 
template <typename T = char>//这里的char为默认模板参数，不允许 
T f(T a, T b) 
{ 
    return a + b; 
};
```
之所以不允许有函数模板的默认模板参数，是因为函数模板的模板参数是在调用的时候编译器根据实参的类型来确定的，如对于上面的第二种情况， 
```cpp
int result = f(1,2)         // int f(int a,int b)，确定T为int 
double result = f(1.0,2.0)  // double f(double a,duoble b))，确定T为double 
```
那么template中的T的缺省值char就毫无意义了，也毫无必要。
但注意，对于上面那些可以通过函数模板的实参演绎模板参数类型的情况，指定缺省的模板实参确实没有意义和必要。但某些情况下，有一些模板参数(比如作为函数的返回值类型)无法通过函数的实参演绎来确定，这个时候指定缺省的模板实参就很有必要，如下情况：
```cpp
// 返回类型RT无法通过实参演绎获得，这个时候指定RT的缺省实参就是非常必要的
template <typename RT, typename T1, typename T2>
inline RT add(T1 a, T2 b){
    return a + b;
}
```

### 9.3 普通（非类模板）类的成员模板
```cpp
// 函数对象类，详见 第14章 的笔记
class DebugDelete {
public:
    DebugDelete(std::ostream &s = std::cerr): os(s) { }
    // 和其它函数模板一样，T的类型由编译器来推断
    template <typename T> void operator()(T *p) const{ 
        os << "deleting unique_ptr" << std::endl; delete p;
    }
private:
    std::ostream &os;
};
```
**使用：**
```cpp
double* p = new double;
DebugDelete d;  // an object that can act like a delete expression
d(p);           // calls DebugDelete::operator()(double*), which deletes p

int* ip = new int;
// calls operator()(int*) on a temporary DebugDelete object
DebugDelete()(ip);
```
**解答：**
(1) `DebugDelete d; d(p);`
&emsp;&emsp; 调用`DebugDelete::operator()`来释放指针`p`；
(2) `DebugDelete()(ip)`
&emsp;&emsp; `DebugDelete()`是新建一个 `DebugDelete临时对象`，然后在这个临时对象上调用`DebugDelete::operator()`来释放指针`ip`；

### 9.4 类模板 的 成员模板
### 9.4.1 如何在类模板中使用 成员模板？
&emsp;&emsp; 对于类模板，我们也可以为其定义成员模板，**在此情况下，类和成员各自有自己的、独立的模板参数**。
对于下面的类模板`Blob`，它的构造函数有自己的慕残类型参数`It`：
```cpp
template <typename T> class Blob {
    template <typename It> Blob(It b, It e); // 注意模板的参数是T，但是构造函数的是It，它们不一样！
    // ...
};
```
### 9.4.2 在类外定义 成员模板 时 要注意什么？
和 类模板的普通成员函数 不一样的是，成员模板是函数模板，因此当我们在类模板外定义一个成员模板时，**必须同时为类模板和成员模板**提供模板参数列表，其中类模板的参数列表在前，后跟成员自己的模板参数列表：
```cpp
template <typename T>   // 类的类型参数
template <typename It>  // 构造函数的类型参数
    Blob<T>::Blob(It b, It e):
        data(std::make_shared<std::vector<T>>(b, e)) { }
```

### 9.4.3 如何使用 类模板的成员模板
&emsp;&emsp; 为了实例化一个类模板的成员模板，我们必须同时提供类、函数模板的实参。
**类模板的实参**：与往常一样，我们在哪个对象上调用成员模板，编译器就根据该对象的类型来推断类模板参数的实参；
**成员模板的实参**：和普通函数模板相同，编译器通常根据传递给成员模板的函数实参来推断他的模板实参。
```cpp
int ia[] = {0,1,2,3,4,5,6,7,8,9};
vector<long> vi = {0,1,2,3,4,5,6,7,8,9};
list<const char*> w = {"now", "is", "the", "time"};

// instantiates the Blob<int> class
// and the Blob<int> constructor that has two int* parameters
// 拆开来解答就是：
//   ① Blob<int> 实例化了 Blob<int>类
//   ② a1(begin(ia), end(ia)) 实例化了接收两个 int*参数的 构造函数
Blob<int> a1(begin(ia), end(ia));

// instantiates the Blob<int> constructor that has
// two vector<long>::iterator parameters
Blob<int> a2(vi.begin(), vi.end());

// instantiates the Blob<string> class and the Blob<string>
// constructor that has two (list<const char*>::iterator parameters
Blob<string> a3(w.begin(), w.end());
```
**解答：**
&emsp;&emsp; **当我们定义`a1`时**，显示指出编译器应该实例化一个`int`版本的`Blob`。而构造函数则根据自己`begin(ia)`和`end(ia)`来推断，结果为`int*`，因此`a1`的定义实例化了`Blob<int>::Blob(int*, int*);`
&emsp;&emsp; **而a2的定义**使用了已经实例化的` Blob<int>`，并用 `vector<long>::iterator`替代参数`It`来实例化构造函数。
&emsp;&emsp; **a3**的定义实例化了一个`string`版本的`Blob`，并实例化了该类的成员模板构造函数，其模板参数绑定到了`list<const char*>。

### 9.4.4 下面这个类 含有 成员模板 吗？不算的话怎么改？
```cpp
template <typename T> class Exp {
public:
    template <typename It> Exp(T b);
private:
    T num;
};
```
**解答：**
&emsp;&emsp; 不算，因为它的成员函数和模板类本身拥有相同的模板参数：`T`，因此不含。改成下面这样就算了：
```cpp
template <typename T1> class Exp {
public:
    template <typename It> Exp(T2 b); // 注意它们模板类和它的构造函数的模板参数不一样，一个是T1，一个是T2
private:
    T num;
};
```






&emsp;
&emsp;
## 10 控制实例化
### 10.1 对于同一个模板，如果用同样的类型参数对其进行实例化，则一定只存在一个实例吗？
&emsp;&emsp; 不一定。当模板被使用时才会被实例化 这一特性意味着，相同的实例可能出现在多个对象文件中。当两个或多个独立编译的源文件使用了相同的模板，并使用了相同的模板参数时，每个文件就都会有该模板的一个实例。

### 10.2 如何解决上面说到的问题呢？
&emsp;&emsp; 在大系统中，在多个文件中实例化相同的模板的额外开销可能非常严重。因此在C++11标准中，我们可以通过 **显示实例化(explicit instantiation)** 来避免这种开销。

### 10.3 使用 显示实例化
#### 10.3.1 语法是怎样的？
一个显示实例化必须有如下形式：
```cpp
extern template declaration; // 实例化声明
template declaration;        // 实例化定义
```
其中`declaration`为 一个类或函数 的声明，**其中所有模板参数需要被替代为模板实参**（注意，需要提供具体的实参，比如`int`），例如：
```cpp
// 注意，下面两个 一个是声明一个是定义！
extern template class Blob<string>;             // 声明
template int compare(const int&, const int&);   // 定义
```
当编译器遇到`extern`模板声明时，它不会在本文件中生成实例化代码。将一个实例化声明为`extern`就表示承诺在程序的其它位置有该实例化的一个非`extern`声明（定义）。对于一个给定的实例化版本，可能有多个`extern`声明，但必须只有一个定义。（这和使用`extern`来声明变量的用法很相似）
**总结**
&emsp;&emsp; 其实这和变量的声明和定义很相似，如果带了`extern`就是声明，不带就是定义。而且光声明不行，必须在后面的代码中提供具体的定义，而且只能定义一次。
&emsp;&emsp; 但是在对模板使用`extern`时，必须提供具体的实参类型，比如`int`:
```cpp
extern template class Blob<string>;  // 正确，这是一个声明
extern template class Blob<T>;       // 错误，必须提供具体的参数类型
```
#### 10.3.2 使用显示实例化时需要注意什么？
&emsp;&emsp; 由于编译器在使用一个模板时自动对其实例化，因此`extern声明`必须出现在任何使用此实例化版本的代码之前：
下面是`Application.cc`：
```cpp
// Application.cc
// 下面两个都是声明，而不是定义，因此必须在其它位置进行实例化
extern template class Blob<string>;
extern template int compare(const int&, const int&);

// 注意！Blob 和 compare 都只是声明，因此必须在其它文件提供定义。
Blob<string> sa1, sa2;  // instantiation will appear elsewhere
// Blob<int> and its initializer_list constructor instantiated in this file
Blob<int> a1 = {0,1,2,3,4,5,6,7,8,9};
Blob<int> a2(a1);       // copy constructor instantiated in this file
int i = compare(a1[0], a2[0]); // instantiation will appear elsewhere
```
下面是`templateBuild.cc`
```cpp
// templateBuild.cc
// 此处包含了Blob 和 compare的定义，其它文件若想使用这两个模板，需要在该文件提供extern声明
template int compare(const int&, const int&);
template class Blob<string>; // instantiates all members of the class template
```
**解答：**
在`Application.cc文件`中，我们使用了类模板`template class Blob<string>`和函数模板`template int compare(const int&, const int&)`，但是在该文件中并没有定义(实例化)这两个模板，因此我们必须在其它文件(`templateBuild.cc文件`)中定义(实例化)这两个模板。

### 10.4 `extern实例化声明`的 类模板 和 正常的实例化定义 时 有何不同呢？为什么？
**(1) 有什么不同？**
&emsp;&emsp; 一个类模板的实例化定义会实例化该模板的所有成员，包括内联的成员函数。因为
**(2) 为什么？**
&emsp;&emsp; 当编译器遇到一个实例化定义时，它不了解程序使用哪些成员函数。因此，与处理类模板的普通实例化不同，编译器会实例化该类的的所有成员，即使我们不使用该成员，它也会被实例化。因此，我们用来显示实例化的一个类模板的类型，必须能用于模板的所有成员。
 实例化的方式| 特点 |
 ---------|----------|
 实例化定义  | 会实例化该模板的所有成员 | 
 普通实例化    | 只会实例化用到的成员 | 


### 总结
&emsp; (1) 对模板的`extern声明`和对变量的`extern声明`类似，如果带了`extern`就是声明，不带就是定义。而且光声明不行，必须在后面的代码中提供具体的定义，而且只能定义一次
&emsp; (2) 对模板进行`extern声明`时必须带上 具体的类型形参，比如:
```cpp
extern template int compare;`(const int&, const int&);
```
&emsp; (3) 在一个类模板的实例化定义中，所用类型必须能用于模板的所有成员函数。（后面有一个习题讲的是这一条规则）

### 10.5 控制实例化 节省开销 的原理是什么？
&emsp;&emsp; 对于含有`extern声明`的模板的文件，编译器不会为其实例化，即不会生成对应的代码，因为编译器知道你已经在其它文件中实例化了这个模板，这些文件会共用同一个实例化的模板，这样就达到了节省开销的目的。


### 10.6 关于 显示实例化的 一些练习
#### 10.6.1 解释下面这些声明的含义：
```cpp
extern template class vector<string>;
template class vector<Sales_data>;
```
**解答：**
&emsp;&emsp; 第一条语句的`extern表明`不在本文件中生成实例化代码，该实例化的定义会在程序的其它文件中。
&emsp;&emsp; 第二条语句用S`ales_data`实例化`vector`，在其它文件中可用`extern声明`此实例化，使用此定义。

#### 10.6.2 假设`Nodefault`是一个没有默认构造函数的类，我们可以显式实例化`vector<NoDefault>`吗？如果不可以，解释为什么。
**解答：**
&emsp;&emsp;  不可以。原因是：当我们显式实例化`vector<Nodefault>`时，编译器会实例化`vector`的所有成员函数，包括它接受容器大小参数的构造函数。`vector`的这个构造函数会使用元素类型的默认构造函数来对元素进行值初始化，而`NoDefault`没有默认构造函数，从而导致编译错误。

#### 10.6.3 对下面每条标签的语句，解释发生了什么样的实例化（如果有的话）。如果一个模板被实例化，解释为什么；如果未实例化，解释为什么没有。
```cpp
template <typename T> class Stack { };

void f1(Stack<char>);       // (a)

class Exercise {
    Stack<double> &rsd;     // (b)
    Stack<int> si;          // (c)
};

int main() {
    Stack<char> *sc;        // (d)
    f1(*sc);                // (e)
    int iObj = sizeof(Stack<string>); // (f)
}
```
**解答：**
&emsp;&emsp; `(a)、(b)、(c)和(f)`分别发生了Stack对char、double、int和string的实例化，因为这些语句都要用到这些实例化的类。
&emsp;&emsp; `(d)、(e)`未发生实例化，因为在本文件之前的位置已经对所需的模板进行了实例化。






&emsp;
&emsp;
## 11 效率与灵活性
&emsp;&emsp; 对模板设计者在设计时所面临的选择，标准库智能指针给出了一个很好的展示。
&emsp;&emsp; `shared_ptr`和`unique_ptr`之间的**明显不同是**它们管理所保存的指针策略 —— 前者给予我们共享指针所有权的能力；后者则独占指针。这一差异对两个类的功能来说是至关重要的。
&emsp;&emsp; 这两个类的**另一个差异是**他们允许用户重载默认删除器的方式:
> 我们很容易地重载一个`shared_ptr`的删除器，只要在创建或者`reset`指针时传递给它一个可调用对象即可。
> 与之相反，删除器的类型是`unique_ptr`对象类型的一部分。用户必须在定义时以显示模板实参的形式提供删除器的类型。因此，对于`unique_ptr`的用户来说，提供自己的删除器就更为复杂。
> 
也就是说，对于`shared_ptr`来说，删除器是可以重载的，类型是在运行时绑定。而`unique_ptr`的删除器不能重载，且是`unique_ptr`类的一部分，在其编译时绑定。
&emsp;&emsp; 如何处理删除器的差异实际上就是这两个类功能上的差异。但是，如我们将要看到的，这一实现策略上的差异可能对性能产生重要影响。

### 11.1 在运行时绑定删除器
&emsp;&emsp; 虽然我们不知道标准库类型是如何实现的，但可以推断出，`shared_ptr`必须能直接访问删除器。即删除器必须保存为一个指针或者一个封装了指针的类（如`function`）。
&emsp;&emsp; 我们可以确定不是将删除器直接保存为一个成员，因为删除器的类型直到运行时才会知道。实际上，在一个`shared_ptr`的生存期中，我们可以随时改变删除器的类型。我们可以使用一种类型的删除器构造一个`shared_ptr`，随后使用`reset`赋予此`shared_ptr`另一种类型的删除器。通常，类成员的类型在运行时是不能改变的。因此，不能直接保存删除器。
&emsp;&emsp; 为了考察删除器是如何工作的，让我们假定`shared_ptr`将它管理的指针保存在一个成员`p`中，且删除器是通过一个名为`del`的成员来访问的。则`shared_ptr`的析构函数必须包含类似下面这样的语句：
```cpp
//del的值只有运行时才知道；通过一个指针来调用它
del ? del(p) : delete p; //del(p)需要运行时跳转到del的地址
```
由于删除器是间接保存的，调用`del(p)`需要一次运行时的跳转操作，转到`del`中保存的地址来执行对应的代码。

### 11.2 在编译时绑定删除器
&emsp;&emsp; 现在，让我们来考察`unique_ptr`可能的工作方式。在这个类中，删除器的类型是类类型的一部分。即，`unique_ptr`有两个模板参数，一个表示它所管理的指针，另一个表示删除器的类型。由于删除器的类型是unique_ptr类型的一部分，因此删除器的成员类型在编译时是知道的，从而删除器可以直接保存在`unique_ptr`对象中。
&emsp;&emsp; `unique_ptr`的析构函数与`shared_ptr`的析构函数类似，也是对其保存的指针调用用户提供的删除器或执行delete。
```cpp
//del在编译时绑定；直接调用实例化删除器
del(p); //无运行时额外开销
```
del的类型是默认删除器类型，或者用户提供的类型。到底是哪种情况没有关系，应该执行的代码在编译时肯定知道。实际上，如果删除器是类似`DebugDelete`之类的东西，这个调用可能被编译为内联形式。
&emsp;&emsp; 通过在编译时绑定删除器，`unique_ptr`避免了间接调用删除器的运行时开销。通过在运行时绑定删除器，`shared_ptr`使用户重载删除器更加方便。






&emsp;
&emsp;
## 12 模板实参推断(templateargument deduction)
### 12.1 什么是模板实参推断？
&emsp;&emsp; 我们知道，对于函数模板，编译器利用调用中的函数实参来确定其模板参数。从函数实参来确定模板实参的过程 被称为 **模板实参推断**。在模板实参推断过程中，编译器使用函数调用中的实参类型来寻找模板实参，用这些模板实参生成的函数版本与给定的函数调用最为匹配。

### 12.2 使用了模板类型参数的形参 在进行参数匹配时 支持类型转换吗？
&emsp;支持，但是**只支持两种转换**：
**(1) const转换** 
> &emsp; 可以将 非`const`对象的引用或者指针 传递给 一个`const`的引用或指针形参。
> 
**(2) 数组或函数指针转换**
> &emsp;如果函数形参不是引用类型（**前提！**），则可以对数组或者函数类型的实参应用正常的指针转换:
> &emsp;&emsp;&emsp; ① 一个数组实参 可以转换为 一个指向其首元素的指针；
> &emsp;&emsp;&emsp; ② 一个函数实参 可以转换为 一个该函数类型的指针。
> 
其它的转换，诸如：算数转换、派生类向基类的转换、用户自定的类型转换 **都不能** 应用于函数模板。
&emsp;&emsp; 作为一个例子，我们来看对函数`fobj()`和`freg`的调用，其中`fobj()`拷贝它的参数，而`freg`的参数是引用类型：
```cpp
template <typename T> T fobj(T, T); // 传值
template <typename T> T fref(const T&, const T&); // 传引用
```
我们来考虑下面几个调用：
```cpp
string s1("a value"); // 非const
const string s2("another value"); // const

fobj(s1, s2); 
fref(s1, s2); 
```
**(1) `fobj(s1, s2); `：**
&emsp;&emsp; 在这个调用中，我们传了一个`string`(s1) 和一个`const string`(s2)，虽然这些类型不严格匹配，但此调用是合法的，因为在`fobj()`是传值，即参数被拷贝，因此原对象是否`const`没有影响。
**(2) `fref(s1, s2); `:**
&emsp;&emsp; 在这个调用中，参数类型都是`const引用`，对于一个引用参数来说，转换为`const`是允许的，因此这个调用也是合法的。
```cpp
// uses premissible conversion to const on s1
int a[10], b[42];
fobj(a, b); // calls f(int*, int*)
fref(a, b); // error: array types don't match
```
**(3) `fobj(a, b); `：**
&emsp;&emsp; 在这个调用中，我们传了数组实参，两个数组大小不同，因此是不同类型。但这不重要，因为两个数组都将被转换为指针，`fobj()`中的模板类型为`int*`.
**(4) `fref(a, b); `:**
&emsp;&emsp; 在这个调用中，`fref()`的形参类型是引用，数组不会转换为指针。（这个在本文的后面有解释）

### 12.3 如何理解 “当行参为引用时，数组不能转换为指针” 这句话？
```cpp
template <typename T> T fref(const T&, const T&){
    
}
```






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
在本文中的 _在类模板中 使用 类的类型成员_ 小结中有讲述。
```cpp

```






&emsp;
&emsp;
## 参考文献
1. [C++:模板实参推断及引用折叠](https://blog.csdn.net/sixdaycoder/article/details/46489891)
2. [怎么样理解:当行参为引用时 数组不能转换为指针](https://bbs.csdn.net/topics/350241300)
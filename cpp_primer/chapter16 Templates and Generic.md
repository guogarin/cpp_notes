
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
&emsp;&emsp; 类模板的成员函数本身是一个普通函数，但是类模板的每个实例都有其自己版本的成员函数，故类模板的成员函数 具有和 类模板 相同的模板参数。因此定义在类模板外的成员函数需以`template`开始，后接与类模板相同的模板参数列表:
```cpp
template <typename T>
ret-type/*返回类型*/ T_Name<T>::member-name/*成员函数名*/(parm-list/*成员函数的形参列表*/)
{
    // 函数体
}
```

### 8.3 用同一个类模板实例化出来的类有关联吗？
&emsp;&emsp; 没有，因为类模板的每个实例都是独立的类。使用不同模板实参实例化出的类之间没有关联，也没有特殊的访问权限。



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
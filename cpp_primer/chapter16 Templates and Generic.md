
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


```cpp

```
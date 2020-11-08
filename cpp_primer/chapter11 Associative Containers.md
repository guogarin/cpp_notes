# 第十一章 关联容器

## 关联容器 和 顺序容器 有何区别？
关联容器 中的元素 是按关键字 来保存和访问的，而顺序容器 中的元素 是根据 它们在容器中的顺序  来保存和访问的。



&emsp;
## 标准库 提供了 哪些关联容器？
关联容器有8个，它们的区别主要体现在3个维度上：
> (1) 是 `map` 还是 `set`
> (2) 同一关键字是否可以重复出现
> (3) 是否按序保存
<div align="center"> <img src="./pic/chapter11/关联容器类型.png"> </div>
<center> <font color=black> <b> 图1 关联容器类型 </b> </font> </center>

其中`map`、`multimap`定义在头文件 `map`中，`set` 和 `multiset`定义在头文件`set` 中
无序容器则定义在 `unordered_map` 和 `unordered_set`中



&emsp;
## map 和 set 有何区别？
`map`中的元素是key-value对，`set`只保存了key



&emsp;
## 如何定义 map 和 set ？
map 需要分别指定 key 和 value 的类型；
set 只需要指定 key 的类型，因为它没有值。
```cpp
map<string, size_t> word_count;
set<string>exclude;
```



&emsp;
## 编写一个程序，从标准输入读单词，统计每个单词出现的次数，并忽略: `"The", "But", "And", "Or", "An", "A","the", "but", "and", "or", "an","a"`
```cpp
int main ()
{
    map<string, size_t> word_count;
    set<string>exclude{"The", "But", "And", "Or", "An", "A","the", "but", "and", "or", "an","a"};
    string word;

    while(cin >> word){
        if(exclude.find(word) == exclude.end()){
            ++word_count[word];
        }
    }

    for(auto &w : word_count){
        cout << w.first << " : " << w.second << endl;
    }

    return 0;
}
```
### 遇到的问题
#### &emsp;1) 如何跳出 `while(cin >> word)`?
&emsp;&emsp;`CTRL` + `d`即可，因为`CTRL` + `d`是EOF



&emsp;
## 关联容器对它的 关键字 有何要求？
#### 有序容器 的要求
&emsp;&emsp;对于**有序关联容器**，其关键字类型 必须定义 元素比较的方法，默认情况下，标准库使用关键字类型的 `<` 运算符来比较两个关键字
# TODO:  
#### 无序容器 的要求



&emsp;
## pair 类型
#### 定义在哪？
&emsp;&emsp; 定义在头文件 `utility` 中
#### 如何定义一个 pair类型?
&emsp;&emsp;创建一个pair类型时，必须指定两个类型名：
```cpp
pair<string, string> anon;
pair<string, size_t> word_count;
```
#### 不给pair类型指定初始值时会发生什么？
&emsp;&emsp;pair将使用默认初始化，即调用pair的默认构造函数进行初始化，但pair的默认构造函数将对数据成员进行值初始化。
#### pair类型 有哪些数据成员？
first 和 second成员。比如对于`pair<string, string> anon{"Micheal", "Jordan"};`来说，`anon.first` 就是 "Micheal"，`anon.second` 就是 "Jordan"
#### pair类型 和其它 关联容器有何 关联？
&emsp;&emsp; map的元素是pair类型的。
#### make_pair()的作用是？原理是？
对于`make_pair(v1, v2)` ：
&emsp;&emsp; 作用：将返回一个用 v1 和 v2 初始化的 pair。
&emsp;&emsp; 原理：pair的类型 将通过 v1 和 v2 的类型 推断得到。
#### 



&emsp;
## 关联容器 有哪些顺序容器没有的 类型别名？
| 类型别名     | 解释                                                                           |
| ----------- | ------------------------------------------------------------------------------ |
| key_type    | 关键字的类型                                                                   |
| mapped_type | 键值 的类型(**只适用于map**)                                                   |
| value_type  | **对于set**，与key_type相同； **对于map**，为pair<const key_type, mapped_type> |
#### &emsp;下面的v1 - v5 分别是什么类型？
```cpp
1. set<string>::value_type v1;         
2. set<string>::key_type v2;           
3. map<string, int>::value_type v3;    
4. map<string, int>::key_type v4;      
5. map<string, int>::mapped_type v5;   
```
结果：
&emsp; 1.  v1 is a string
&emsp; 2.  v2 is a string
&emsp; 3.  v3 is a pair<const string, int>
&emsp; 4.  v4 is a string
&emsp; 5.  v5 is an int



&emsp;
## 解引用关联容器的迭代器将得到什么？
&emsp;&emsp;将得到一个 类型为容器的 value_type 类型 的值的引用



&emsp;
## 操作 map 时需要注意什么？
&emsp;&emsp; map的 关键字的类型 是 `const` 的，因此不能修改map的关键字的值：
```cpp
map<string, string> authors = { {"Joyce", "James"}, {"Austen", "Jane"}, {"Dickens", "Charles"} };
auto map_it = authors.begin();
cout << (*map_it).first; // 正确，输出key的值
cout << map_it->first;   // 正确，输出key的值
map_it->first = "Lebron"; // 错误，关键字是const的，不能修改；
map_it->second = "jack"  // 正确，可以修改 元素的值
```



&emsp;
## 操作 set 时需要注意什么？
&emsp;&emsp;虽然 `set` 同时定义了 `iterator` 和 `const_iterator` 两种迭代器类型，但是 `iterator` 也只能读取 `set` 中的元素，因为和`map`一样，`set` 中的关键字也是 `const`的：
```cpp
set<int> iset = {1, 2, 3, 4, 5};
auto set_it = iset.begin();
if(set_it != iset.end()){
    *set_it = 42; // 错误，set中的关键字是 const 的，不能修改。
}
```



&emsp;
## 对 关联容器 使用 泛型算法 时需要注意什么？
&emsp;&emsp; 一般不建议对 关联容器使用 泛型算法，因为：
1) map 和 set 的关键字都是`const`的，这就意味着不能将关联容器传递给 **修改** 或 **重排** 元素的算法。
2) 虽然关联容器可以用于 只读取元素的算法，但是这些算法一般都要搜索序列，因为关联容器不能通过它们的关键字进行快速查找，所以效率很低，远远比不上 关联容器专用的算法。
3) 在实际应用中，我们一般将关联容器作为一个源位置 或 目的位置，比如可以使用`copy()`算法 从关联容器 拷贝到 另一个序列。



&emsp;
## 往map插入元素有哪几种方式？它们有何区别？
一共有五种方式：
其中四种是以不同的方法调用 `insert()`。
对于map来说，因为`map.insert()`的形是 pair类型，因此这四种方法指的是通过四种不同的方法来构造 一个pair，然后插到map里面，也就是说这几种方法 **本质上 都是 往map中插入一个 pair类型**：
1) 传一个 **value_type** 给insert()     (map的value_type是 pair类型)
2) 传一个 **pair 类型** 给insert()      (直接传 pair类型)
3) 利用**make_pair** 构造一个pair  
4) 传一个 **花括号的值**                (用花括号构造 pair类型)
```cpp
// four ways to add word to word_count
word_count.insert(map<string, size_t>::value_type(word, 1));    // 传一个 **value_type** 给insert()
word_count.insert(pair<string, size_t>(word, 1));               //  传一个 **pair 类型** 给insert()
word_count.insert(make_pair(word, 1));                          // 利用**make_pair** 构造一个pair 
word_count.insert({word, 1});                                   // 传一个 花括号的值(用花括号构造 pair类型)
```
剩下的一中是以数组的形式插入：
以数组的形式插入时，若map中有这个关键字，此时insert 操作是无法插入的，但是用这种方式会直接覆盖掉原先的数据。
```cpp

```

&emsp;
## STL中的map 和 python的map 有何异同？


&emsp;
## 关联容器的底层原理
&emsp;&emsp;类似于顺序容器，关联容器也是模板。
### 1) set的底层原理？

&emsp;
### 2) map的底层原理

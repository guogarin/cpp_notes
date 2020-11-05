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

其中`map`、`multimap`定义在头文件 `map.h`中，`set` 和 `multiset`定义在头文件`set.h` 中
无序容器则定义在 `unordered_map.h` 和 `unordered_set.h`中



&emsp;
## map 和 set 有何区别？
`map`中的元素是key-value对，`set`只保存了key



&emsp;
## set的底层原理？



&emsp;
## map的底层原理

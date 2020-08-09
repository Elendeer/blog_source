---
title: STL
date: 2020-08-09 16:22:40
tags: STL
---

这段时间噬月在进行数据结构和算法的复习和学习！

这篇博客将包括C++STL的简单介绍。

<!--more-->

本篇算法模板来自AcWing的y总。

## STL

关于STL的讲解是老生常谈，而且比较机械；本篇博客只是希望梳理一下一些常用的模板，并没有剖析源码的打算（主要是因为菜），因此就简单罗列一下各个模板。

### vector

变长数组，倍增的思想。

拥有下列方法：

```notes
    size()  返回元素个数
    empty()  返回是否为空
    clear()  清空
    front()/back()
    push_back()/pop_back()
    begin()/end()
    []
    支持比较运算，按字典序
```

### pair<int, int>

不一定装`int`，标题中以`int`为例。

拥有下列方法：

```notes
    first, 第一个元素
    second, 第二个元素
    支持比较运算，以first为第一关键字，以second为第二关键字（字典序）
```

### string

字符串。

拥有下列方法：

```notes
    size()/length()  返回字符串长度
    empty()
    clear()
    substr(起始下标，(子串长度))  返回子串
    c_str()  返回字符串所在字符数组的起始地址
```

### queue

队列。

拥有下列方法：

```notes
    size()
    empty()
    push()  向队尾插入一个元素
    front()  返回队头元素
    back()  返回队尾元素
    pop()  弹出队头元素
```

### priority_queue

优先队列，默认是大根堆。

拥有下列方法：

```notes
    push()  插入一个元素
    top()  返回堆顶元素
    pop()  弹出堆顶元素
```

定义成小根堆的方式：`priority_queue<int, vector<int>, greater<int>> q;`

### stack

栈。

拥有下列方法：

```notes
    size()
    empty()
    push()  向栈顶插入一个元素
    top()  返回栈顶元素
    pop()  弹出栈顶元素
```

### deque

双端队列。

拥有下列方法：

```notes
    size()
    empty()
    clear()
    front()/back()
    push_back()/pop_back()
    push_front()/pop_front()
    begin()/end()
    []
```

### set, map, multiset, multimap

基于平衡二叉树（红黑树），动态维护有序序列。

拥有下列方法：

```notes
    size()
    empty()
    clear()
    begin()/end()
    ++, -- 返回前驱和后继，时间复杂度 O(logn)

    set/multiset
        insert()  插入一个数
        find()  查找一个数
        count()  返回某一个数的个数
        erase()
            (1) 输入是一个数x，删除所有x   O(k + logn)
            (2) 输入一个迭代器，删除这个迭代器
        lower_bound()/upper_bound()
            lower_bound(x)  返回大于等于x的最小的数的迭代器
            upper_bound(x)  返回大于x的最小的数的迭代器
    map/multimap
        insert()  插入的数是一个pair
        erase()  输入的参数是pair或者迭代器
        find()
        []  注意multimap不支持此操作。 时间复杂度是 O(logn)
        lower_bound()/upper_bound()
```

### unordered_set, unordered_map, unordered_multiset, unordered_multimap

哈希表。

拥有的方法和上面类似，增删改查的时间复杂度是 O(1)。不支持`lower_bound()/upper_bound()`，以及迭代器的自增自减。

### bitset

圧位。

所谓压位，个人理解为当bool数组占用空间过大使用它来减小空间开支——毕竟，顾名思义，“bitset”作为“比特集”，每个元素只占用一个二进制位（而bool占用一个字节，即八个二进制位）。声明方式：`bitset<10000> s;`

支持下列操作/方法：

```notes
    ~, &, |, ^
    >>, <<
    ==, !=
    []

    count()  返回有多少个1

    any()  判断是否至少有一个1
    none()  判断是否全为0

    set()  把所有位置成1
    set(k, v)  将第k位变成v
    reset()  把所有位变成0
    flip()  等价于~
    flip(k) 把第k位取反
```

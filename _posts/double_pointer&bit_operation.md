---
title: double_pointer&bit_operation
date: 2020-07-16 09:19:19
tags: algorithm
---

这段时间噬月在进行算法的复习和学习！

这篇博客将包括双指针算法以及两种常用的位运算操作。

<!--more-->

本篇算法模板来自AcWing的y总。

## 双指针算法

一般分为两类：

1. 两个指针分别指向两个数组做处理
2. 两个指针指向同一个数组做处理

*联想：归并排序和快速排序。参考博文sort。*

双指针算法一般长成这样：

```cpp
for (int i = 0, j = 0; i < n; ++ i ) {
    while (j < i && check(i, j)) {
        ++ j;
    }
    // 后面是题目的具体逻辑
}
```

或者类似的样子。

双指针算法的核心思想：将形如双重for嵌套的暴力遍历算法（$O(n^2)$）优化成$O(n)$。在写题时，可以先写一个暴力算法，然后通过观察之间有没有单调关系，来进行双指针优化。

双指针算法的概念比较广泛，可以看一题[最长连续不重复子序列](https://www.acwing.com/problem/content/801/)作为例子，题解如下：

```cpp
#include <iostream>
#include <cstdio>

using namespace std;

const int N = 1e5 + 10;
int a[N], b[N];

int main() {
    int n;
    cin >> n;
    for (int i = 0; i <= n - 1; ++ i ) {
        scanf("%d", &a[i]);
    }
    int j = 0, res = 0;
    for (int i = 0; i <= n - 1; ++ i ) {
        b[a[i]] ++ ;
        while (j <= i && b[a[i]] > 1) {
            b[a[j]] -- ;
            j ++ ;
        }
        res = max(res, i - j + 1);
    }
    cout << res;
    return 0;
}
```

这题同样有暴力解法，亦即是两重`for`循环枚举所有可能的$[j, i]$区间，一个个计数并判断。

其中的单调关系体现在：设区间$[j, i]$内有重复元素，则区间$[j - 1, i]$内必定也有。这意味着在枚举j的位置时，j从不用后退。于是可以把双重`for`优化成单个`for`枚举i，并用`while`及时更新j的位置。使用了一个体现了映射思想的数组b，令`b[n]`记录数字n出现的次数，一旦右端点指向的数字曾经出现过（即`b[a[i]] > 1`），便令j前进直到抛出这个重复出现的数。优化的结果是时间复杂度从$O(n^2)$降低到$O(n)$，思路和成效可以管中窥豹。

---

## 位运算常用操作

### $n$的二进制表示中第$k$位是几

例：$n = 15 = (1111)_2$

1. 把第$k$位移到最后一位`n >> k`
2. 看个位是几`x & 1`

合起来亦即是`n >> k & 1`。

### lowbit

`lowbit`是树状数组的一个基本操作，`lowbit(x)`作用是返回x的最后一位1。

例：

$x = 1010, lowbit(x) = 10;$
$x = 101000, lowbit(x) = 1000;$

怎么实现的呢？简单一句话：`x & -x`。

C++里，负数是原数的补码，亦即取反加一。`x & -x = x & (~x + 1)`，于是：

```note
x               = 1010 ... 100 ... 0
~x              = 0101 ... 011 ... 1
~x + 1          = 0101 ... 100 ... 0
x & (~x + 1)    = 0000 ... 100 ... 0
```

应用：统计x中1的个数。

十分简单的样板题[二进制中1的个数](https://www.acwing.com/problem/content/803/)

```cpp
#include <iostream>
#include <cstdio>

using namespace std;

const int N = 1e5 + 10;
int a[N];

int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; ++ i ) {
        scanf("%d", &a[i]);
    }
    for (int i = 0; i < n; ++ i ) {
        int res = 0;
        while (a[i]) {
            a[i] -= a[i] & -a[i];
            res ++ ;
        }
        printf("%d ", res);
    }
    return 0;
}
```

题解也十分简单粗暴，查出一个1就减掉对应的数（对二进制数来说，该1就被削成0了）直到削掉最后一个1，res就统计出了结果。

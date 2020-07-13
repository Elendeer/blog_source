---
title: sort
date: 2020-07-08 18:46:56
tags: algorithm
---

这段时间噬月在进行算法的复习和学习！

这篇博客将包括两个经典的排序算法：快速排序和归并排序。

<!--more-->

## 1. 快速排序——分治

设有待排序区间从l到r（即数组q的左右端点）：

1. 确定分界值x：$q[l]$或$q[(l+r) / 2]$或$q[r]$或随机位置的值
2. **调整区间**：小于等于分界值x的数在左，所有大于等于分界值x的数在右
3. 递归处理左右两段

```notes
注：
第一步分界点x的取法会影响到边界问题的解决方式
详见下文
```

第二步**调整区间**的处理方法：

---

暴力：

1. 开数组a，b
2. 遍历数组q，将小于等于x的数插入a，大于x的数插入b
3. 将ab依次填入q

不是很优美，但时间复杂度依然是$O(n)$，然而使用了两块额外的空间。

---

优美的方法：

使用两个指针，$i$指向左边，$j$指向右边。两个指针向中间靠拢。

先动$i$，若$i$指向的数小于等于x，则不做操作，继续移动$i$；若大于x，转而移动$j$；

$j$向左移动，若指向的数大于x，则不做操作，继续移动$j$；若小于等于x，则与$i$指向的数交换。然后继续移动$i$，重复上述过程，直到两个指针相遇。相遇处为新区间的端点。

---

代码模板：

```cpp
#include <iostream>
#include <cstdio>

using namespace std;

const int N = 1e6 + 10;

int n;
int q[N];

void quick_sort(int q[], int l, int r) {
    if (l >= r) {
        return;
    }

    /**
    注1
     */
    int x = q[(l + r) / 2], i = l - 1, j = r + 1;

    while (i < j) {
        do {
            ++i;
        } while (q[i] < x);
        do {
            --j;
        } while (q[j] > x);

        if (i < j) {
            swap(q[i], q[j]);
        }
    }

    quick_sort(q, l, j);
    quick_sort(q, j + 1, r);
}

int main() {
    scanf("%d", &n);
    for (int j = 0; j <= n; ++j) {
        scanf("%d", &q[j]);
    }

    quick_sort(q, 0, n - 1);

    for (int j = 0; j <= n; ++j) {
        printf("%d ", q[j]);
    }
    return 0;
}

```

注1：

- $i$和$j$的初始化带有偏移量，是因为do-while会不由分说先向中间移动指针（为了方便）；

- x值的设定须满足：

  - 取$q[r]$时不用 `quick_sort(q, l, i - 1);quick_sort(q, i, r);`，因为此时$i$一定会停在最左边，$[i, r]$区间一定和原区间相等，导致无限递归（当$i$和$j$有机会重合时，`quick_sort(q, l, j - 1);quick_sort(q, j, r)`也会导致相应的问题）。

  - 取$q[l]$时不用 `quick_sort(q, l, j);quick_sort(q, j + 1, r);`，因为此时$j$一定会停在最右边，$[l, j]$区间一定和原区间相等，导致无限递归（当$i$和$j$有机会重合时，`quick_sort(q, l, i);quick_sort(q, i + 1, r)`也会导致相应的问题）。

    实质上不是字母的问题，而是边界加减的问题。

  - 取$q[(l + r) / 2]$时，当区间长度为1（递归到底时必定出现这样的区间）等同于取$q[l]$，这在数学上很容易看出来。因此边界的处理也应当遵循上述第二点。

  - 随机x的方法也应避免取到边界，目前个人想到的做法是在$(r - 1) - (l + 1) \gt 1$时，在区间$[l + 1, r - 1]$内随机；而当$(r - 1) - (l + 1) \le 1$时使用$q[l]$。

---

## 2. 归并排序——分治

与快排的以值为界不同，归并排序的分治以数组**位置**（即下标）为界。

设有待排序区间从l到r（即数组q的左右端点）：

1. 确定分界值mid = (l + r) / 2，以mid把数组分为左右两部分。
2. 递归排序左右区间。
3. **归并**——合二为一。这一步时间复杂度是$O(n)$。

如何归并：

**双指针算法**：

1. 创建两个指针分别指向两个有序数组的小端；
2. 比较两个所指数，取出较小的一个（如果两个数相等，那么习惯上取前一个），并使对应的指针前进一位；
3. 判断是否到头，如果其中一个数组已经取完，那么依次取出另一个数组里的数；
4. 反复执行，直到所有数依次取出。

```notes
一个小知识点：排序是否“稳定”

排序是否稳定不是指时间复杂度。

如果一个排序算法执行后，值相等的数的位置可能发生改变，
则称这个排序算法是“不稳定的”，
否则称“稳定的”。

快排是不稳定的，归并是稳定的。
但若把快排整成pair，使得被排序对象各不相同，则快排也可以成为稳定的。

然而这个知识点并没什么卵用。
```

从区间的分治策略和归并步骤的时间复杂度可知，归并排序的时间复杂度是$O(n \log_2 n)$

---

代码模板：

```cpp
#include <iostream>
#include <cstdio>

using namespace std;

const int N = 1e6 + 10;
int temp[N];
int q[N];

void merge_sort(int q[], int l, int r) {
    if (l >= r) {
        return ;
    }

    // 位运算求平均
    int mid = (l + r) >> 1;
    int i = l, j = mid + 1;

    merge_sort(q, l, mid);
    merge_sort(q, mid + 1, r);

    // k用于表示已取出的数的数量
    int k = 0;
    while (i <= mid && j <= r) {
        if (q[i] < q[j]) {
            temp[k ++] = q[i ++];
        }
        else {
            temp[k ++] = q[j ++];
        }
    }
    while (i <= mid) {
        temp[k ++] = q[i ++];
    }
    while (j <= r) {
        temp[k ++] = q[j ++];
    }

    for (int i = 0, j = l; i <= k - 1; ++ i, ++ j) {
        q[j] = temp[i];
    }
    // for (int i = l, j = 0; i <= r; ++ i, ++ j) {
    //     q[i] = temp[j];
    // }
}

int main() {
    int n;
    scanf("%d", &n);
    for (int j = 0; j <= n - 1; ++j) {
        scanf("%d", &q[j]);
    }

    merge_sort(q, 0, n - 1);

    for (int j = 0; j <= n - 1; ++j) {
        printf("%d ", q[j]);
    }

    return 0;
}
```

---

在边界问题上应当留心理解，这样有利于记忆。或者直接背下来。

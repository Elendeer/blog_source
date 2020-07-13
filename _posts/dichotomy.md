---
title: dichotomy
date: 2020-07-11 21:10:00
tags: algorithm
---

这段时间噬月在进行算法的复习和学习！

这篇博客将包括两个二分算法：整数二分和浮点数二分。

<!--more-->

## 整数二分

二分的本质并不是单调性，有单调性概念的题目一定可以二分，但可以二分的题目不一定有所谓的单调性。

假如有一个区间，可以按照具有/不具有某种性质来划分成两个部分，就可以使用二分法求出边界点。

整数区间是离散的。

设有一个整数区间$[l, r]$可分成$[l, x]$和$[x + 1, r]$，另一种分法是$[l, x - 1]$和$[x, r]$，前者的$x$是左区间的右端点，后者的$x$是右区间的左端点。两个不同端点的程序模板略有差异。

前者：

1. mid = (l + r + 1) / 2
2. if (check(mid))
   1. true: $x$在$[mid, r]$中，更新l = mid
   2. false: $x$在$[l, mid - 1]$中，更新r = mid - 1
3. 递归/循环

后者：

1. mid = (l + r) / 2
2. if (check(mid))
   1. true: $x$在$[l, mid]$中，更新r = mid
   2. false: $x$在$[mid + 1, r]$中，更新l = mid + 1
3. 递归/循环

以下是两个模板：

```cpp
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

---

为什么前者要补上+1：

令l = r - 1，则check(mid)为true时，更新：l = mid = r（整数除法下取整），发生死循环。

---

如何考虑使用那种模板：

先写（或设想）check函数（的边界）（不一定要写成函数，可以直接写在if条件里），然后随便写一种二分法，思考如何寻找点和更新边界；

如果是l = mid更新，则mid的求法添上+1，如果是r = mid则不用。

---

## 浮点数二分

由于浮点数可以近似看成连续的（数学上的实数当然是严格连续的），那么就没有整数二分的边界问题。不需要考虑边界，浮点数二分就变得更加简单。思路则和整数二分是一样的，同为二分法。

解区间可以二分到很小，至于多小就得看我们希望它多小，并把我们的（一般来说也是题目的）要求写进`while`循环的条件里。

例：开方

```cpp
#include <iostream>
#include <cstdio>

int main() {
    using namespace std;
    double x;
    cin >> x;

    double l = 0, r = x;

    while (r - l >= 1e-6) {
        double mid = (l + r) / 2;
        if (mid * mid > x) {
            r = mid;
        }
        else {
            l = mid;
        }
    }

    printf("%lf", l);

    return 0;
}
```

最后的输出l或r都可以。上述程序输入4有可能不会得到2，而是1.999999，这是`while`循环条件造成的精度问题，将其改为`r - l > 1e8`则可以得到2.000000

一条关于精度的经验之谈：若题目要求保留5位小数，则写1e-7，保留6位则写1e-8，总之while条件至少应该比要求的精度小两个数量级。

也有另外一种比较神奇的写法：

```cpp
#include <iostream>
#include <cstdio>

int main() {
    using namespace std;
    double x;
    cin >> x;

    double l = 0, r = x;

    for (int j = 1; j <= 100; ++ j) {
        double mid = (l + r) / 2;
        if (mid * mid > x) {
            r = mid;
        }
        else {
            l = mid;
        }
    }

    printf("%lf", l);

    return 0;
}
```

此法叫做：管他丫的三七二十一迭代它一百次再说。

一般也比较有效。

---

## 一些废话

while的条件描述了“二分精度”，而整数二分到最后两个端点必定会相遇，无所谓精度问题；浮点数二分则有精度问题。

while中的if通过条件判断（亦即是check函数）来描述二分的过程中如何更新区间的端点，整数二分的边界问题需要留心注意处理方式。

## 实例

[AcWing的一个不错的整数二分题](https://www.acwing.com/problem/content/791/)

题解：

```cpp
#include <iostream>
#include <cstdio>

const int N = 100010;
int a[N];

int main() {
    using namespace std;

    int n, q;
    scanf("%d%d", &n, &q);
    for (int j = 0; j <= n - 1; ++ j) {
        scanf("%d", &a[j]);
    }

    for (int j = 1; j <= q; ++ j) {
        int k;
        scanf("%d", &k);

        int l = 0, r = n - 1;

        while(l < r) {
            int mid = l + r >> 1;
            if (a[mid] >= k) {
                r = mid;
            }
            else {
                l = mid + 1;
            }
        }

        if (a[r] != k) {
            cout << "-1 -1" << endl;
        }
        else {
            cout << l << " ";
            r = n - 1;
            while (l < r) {
                int mid = l + r + 1 >> 1;
                if (a[mid] <= k) {
                    l = mid;
                }
                else {
                    r = mid - 1;
                }
            }
            cout << l << endl;
        }

    }
    return 0;
}
```

这里需要注意check函数的写法，写`a[mid] >= k`而不是`a[mid] < k`正是因为需要求得右区间的左端点，而后者会求得左区间的右端点。这自然是一开始蒟蒻噬月踩的坑。

两个二分求端点中间的if则是一个判断是否找到数k的一个小技巧。

---
title: hash
date: 2020-08-07 12:04:18
tags: data_structure algorithm
---

这段时间噬月在进行数据结构和算法的复习和学习！

这篇博客将包括哈希表以及字符串哈希算法（字符串前缀哈希法）。

<!--more-->

本篇算法模板来自AcWing的y总。

## 哈希表

*哈希表又称**散列表**，是一个可以根据键值访问存储位置（元素）的数据结构。* 亦即，它通过一个可以计算出键值的函数，将所需查询的数据映射到表中一个位置来访问和记录，由于使用函数来映射位置而不是粗暴地遍历整个表，这样的操作无疑可以加快查找速度。这个映射函数称做**散列函数**或**哈希函数**，存放记录的数组称做**散列表**或**哈希表**。

Wiki举了一个很形象的例子，这里引用一下：

> 一个通俗的例子是，为了查找电话簿中某人的号码，可以创建一个按照人名首字母顺序排列的表（即建立人名$x$到首字母$F(x)$的一个函数关系），在首字母为W的表中查找“王”姓的电话号码，显然比直接查找就要快得多。*这里使用人名作为关键字（即所谓键值），“取首字母”是这个例子中散列函数的函数法则$F$，存放首字母的表对应散列表。* 关键字和函数法则理论上可以任意确定。
> 以上引自Wiki。

从上述例子不难对哈希表及其功能有一个感性的认知。接下来我们会讨论具体的问题和思路，以及代码实现的具体方式。

```notes
注：曾经在博文里讨论过的离散化是一种特殊的哈希方式。
```

---

首先是如何选取哈希函数的问题。具体的问题可以设计不同的哈希函数，对于同一问题的不同哈希函数而言，优劣之分是确实存在的。而对于我们在解题时最常见的情况——把一些分布范围大的数映射到较窄的区间里——而言，模上一个数是最常见也是最方便的选择。

我们还需要考虑一个几乎所有哈希表都需要考虑到的问题：映射之后的冲突问题。什么意思呢？即：两个不同的数，模上一个数之后值相等，怎么办？对于Wiki那个例子来说就是：两个不同电话号码的主人都姓王，怎么办？我们需要解决这样的冲突。解决冲突常用的方法有两个：**拉链法**和**开放寻址法**。

拉链法如何处理冲突呢？它会在遇到冲突元素时在该节点按照插入顺序拉出一条链表，询问元素时则遍历映射后的对应链表即可（这时候就需要和哈希前的数进行比较，而不是完全依赖于键值）。在一般情况下，链表的长度都不会很长，可以看作常数。

开放寻址法如何处理冲突呢？它会在遇到冲突元素时向周边某个寻找空间，如果依然没有空间，就继续向这个方向寻找下去，直到找到空间再存放。这时我们就会发现，开放寻址法好像有那么一点不和谐：元素最终放置的位置可能和它哈希完的键值不一样，存在一定的偏移。那么这种偏移会不会造成查询的错误呢？实际上是不会的，开放寻址法在查询的时候同样也不完全依赖键值，它会向储存时寻址的方向持续寻找，并与哈希前的数比对，直到找到元素，或者找不到连续储存的数了。

![hash](https://user-images.githubusercontent.com/61586472/89728880-1a889e80-da63-11ea-9853-2794751a4439.png)

而不管用哪一种方法，哈希表的操作都可以近似认为是$O(1)$的。

```notes
注：在算法题中一般只涉及插入和查询，而不涉及删除。
若有需要删除，则在下面的实现中加入禁用元素的标志和方法即可。
```

那么开放寻址法有没有可能一直存在连续的数导致时间复杂度暴涨呢？亦或是储存的元素太多导致把空间填满，使得开放寻址法寻不到地方了呢？事实上我们在实现的时候会规避这些可能的bug，下面可以以一题为例来观察一下两个不同的方法。

---

我们可以看AcWing上的一个例题[模拟散列表](https://www.acwing.com/problem/content/842/)

在此题中，我们需要维护一个数集，为方便插入和查询，可以考虑使用哈希表来做。而一般在做哈希的时候，我们倾向于把要模的数取成一个质数，并且离2的整次幂尽可能远，这可以降低冲突几率。我们可以使用以下代码来求得离$N$最近且大于$N$的一个质数。

```cpp
for (int i = N;; ++ i ) {
    bool flag = true;
    for (int j = 2; j * j <= i; ++ j ) {
        if (i % j == 0) {
            flag = false;
            break;
        }
    }

    if (flag) {
        cout << i << endl;
        break;
    }
}
```

拉链法

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>

using namespace std;

const int N = 1e5 + 19;

int h[N], e[N], ne[N], idx;

// insert函数可以结合静态链表博客辅助理解
void insert(int x) {
    // 计算哈希值k，加N模N防止出现负数
    int k = (x % N  + N ) % N;
    // 创建新的节点放入x值
    e[idx] = x;
    // 新节点的指针指向h[k]指向的节点
    ne[idx] = h[k];
    // 令h[k]指向当前节点，并使idx自增到新的未使用空间
    h[k] = idx ++ ;
}

bool find(int x) {
    // 计算哈希值k
    int k = (x % N + N) % N;
    // 遍历对应位置的链表
    for (int i = h[k]; i != -1; i = ne[i]) {
        // 找到
        if (e[i] == x){
            return true;
        }
    }
    // 没找到
    return false;
}

int main() {
    int n;
    cin >> n;

    memset(h, -1, sizeof(h));
    // 表中所有元素一开始都指向-1，即指向空节点

    while (n -- ) {
        char op[2];
        int x;
        scanf("%s%d", op, &x);
        if (*op == 'I') {
            insert(x);
        }
        else {
            if (find(x)) puts("Yes");
            else puts("No");
        }
    }

    return 0;
}
```

*注：在拉链法中，`h[N]`中存放的是模拟指针（即静态链表博文中讨论过的那种），而不是直接存放数据。*

---

开放寻址法

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>

using namespace std;

// 开两倍（或三倍）以降低冲突概率，并取最近的质数
// null是我们约定的空位标志，需要在数据范围之外
const int N = 2e5 + 3, null = 0x3f3f3f3f;

int h[N];

// 如果找到x，返回其位置；否则返回一个可用的空位
int find(int x) {
    int k = (x % N + N) % N;
    while (h[k] != null && h[k] != x) {
        k ++ ;
        if (k == N) k = 0;
    }

    return k;
}

int main() {

    // memset按照字节填充值，一个整型占四个字节
    // 因此填充0x3f会使得数组内所有int数都是0x3f3f3f3f
    memset(h, 0x3f, sizeof(h));

    int n;
    cin >> n;
    while (n -- ) {
        char op[2];
        int x;
        scanf("%s%d", op, &x);

        int k = find(x);
        if (*op == 'I') {
            h[k] = x;
        }
        else {
            if (h[k] != null) puts("Yes");
            else puts("No");

        }
    }
    return 0;
}
```

两种方法都比较常用，但开放寻址法的代码较短，也只需要开一个数组。

## 字符串前缀哈希法

假如我们现在有一个字符串`str = "ABCDEF"`，我们现在预处理出它的所有前缀哈希：

```notes
h[0] = 0
h[1] 为 "A" 的前缀哈希
h[2] 为 "AB" 的前缀哈希
h[3] 为 "ABC" 的前缀哈希
h[4] 为 "ABCD" 的前缀哈希
h[5] 为 "ABCDE" 的前缀哈希
...
```

那么如何来得到一个字符串的哈希值呢？我们这里先将字符串看作一个$P$进制的数，每一个字母是一个数字（按照$ASCII$码排序）。

举个例子：字符串`ABCD`，我们将它视为$P$进制的`1234`（实际上我们实现时会直接把每个字符看成ASCII码的int值，这相当于把字符看成“ASCII码那么多位数的进制数表示的P进制数”，有点绕，但确实可行），此时它应该等于十进制的

$$ (1 \times P^3 + 2 \times P^2 + 3 \times P^1 + 4 \times P^0) $$

这很合理，不过还有一个小问题需要解决：我们的字符串可能很长，因此我们可能得到一个相当长的数，为此我们需要让上式$mod$上一个数$Q$，从而把一个字符串映射到$[0, Q - 1]$的区间上。

OK，这里有几个值得注意的地方：

1. 不能把某个字符——比如叫字符$c$，映射成$P$进制0——否则字符串 `c`将和字符串`cc`拥有相同的哈希值。
2. 第二个点也有点像给第一个点解释：我们的做法中实际上是假设我们运气足够好，不会遇到任何一个冲突（也就是没有任何两个字符串哈希值相等）。这里又有玄学的经验值了：当我们的$P = 131 | 13331, Q = 2^{64}$时，在大多数情况下都不会有冲突。
    - 这里有一个小tip：我们在实现时可以使用`unsigned long long`作为哈希数组的类型，这样当计算数值溢出时，就相当于$mod$了一个$2^{64}$。

实现也相当简单：写作 `h[i] = h[i - 1] * P + str[i]`即可。

那么将字符串哈希成数字的内容讲完了，这个处理方法有什么用呢？答案是：我们可以利用我们处理出来的某个字符串的前缀哈希值，使用如下公式计算出该字符串的任意子串的哈希值。

$$ 设字符串str中有两个位置L和R, L < R \\ 欲求从L到R的子串的哈希值res \\ 则 res = h[R] - h[L - 1] \times P^{R - L + 1}$$

为什么可以这样做呢？很简单：`h[L - 1]`和`h[R]`分别是以$L - 1$和$R$结尾的前缀字符串的哈希值，最高位相差了$L - R + 1$个$P$进制位；而$h[L - 1] \times P^{R - L + 1}$正是先把位数交少的`h[L - 1]`拔到与`h[R]`平齐，再做减法，自然得到了从$L$到$R$的子串的哈希值。

![string_hash](https://user-images.githubusercontent.com/61586472/89728884-1d838f00-da63-11ea-9050-606665e81b6b.png)

如何得到任意子串的哈希值的内容也讲完了，那么这又有什么用呢？其实提到*前缀哈希*和*任意子串*，我们可能会想到一个熟悉的东西叫做*KMP字符串匹配算法*——没错，在大部分情况下字符串哈希都可以完成KMP可以完成的事情，而且更加的简单。

举个例子，我们可以看一下这个[询问字符串中任意两个子串是否相等的题](https://www.acwing.com/problem/content/843/)，这个题使用字符串哈希显然比KMP来得方便。

解法放在下面：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>

using namespace std;

typedef unsigned long long ULL;

const int N = 1e5 + 10, P = 131;
int n, m;
char str[N];
ULL h[N], p[N]; // p数组预处理出p的n次幂

ULL get(int l, int r) {
    return h[r] - h[l - 1] * p[r - l + 1];
}

int main() {
    scanf("%d%d", &n, &m);
    scanf("%s", str + 1);

    p[0] = 1;
    for (int i = 1; i <= n; ++ i ) {
        h[i] = h[i - 1] * P + str[i];
        p[i] = p[i - 1] * P;
    }

    while (m -- ) {
        int l1, r1, l2, r2;
        scanf("%d%d%d%d", &l1, &r1, &l2, &r2);

        if (get(l1, r1) == get(l2, r2)) puts("Yes");
        else puts("No");
    }
    return 0;
}
```

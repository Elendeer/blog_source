---
title: discretization&interval_merge
date: 2020-07-16 21:56:24
tags: algorithm
---

这段时间噬月在进行算法的复习和学习！

这篇博客将包括离散化和区间合并。

<!--more-->

本篇算法模板来自AcWing的y总。

## 离散化

先说是什么：

考虑一个这样的问题：有一个无限长的数组，元素初始值都为0，现在在上面随机插入5个数，然后随机询问一个区间$[i, j]$，我们需要回答出该区间中所有元素的和。我们应该怎么做？

这个数组是无限长的，我们总不能真开个无限长的数组吧？否则内存可能直接现场爆炸以抒发激动之情。那怎么办？我们会发现，尽管这个数组是无限长的，但我们从头到尾用到的坐标最多只有7个，分别是5个元素和两个区间坐标（如果询问的坐标恰好在插入元素时使用过，那就会更少），其他的坐标上的元素，初始值是零，显然对求和没有影响。那么我们就可以考虑，*先读入所有用得到的坐标，然后令这些坐标根据大小排序（使得其原来在数轴上的大小顺序不变），然后将这些坐标依次映射到一个短数组上*。当无限长的、元素稀疏的数组变为短的、元素密集数组时，这个问题的解法就很明了了——使用我们讨论过的**前缀和**算法（公式）就可以了。*而以上做映射的过程，就称为离散化*。

**离散化是映射的过程**，这里只讨论整数的有序离散化。假如我们有一个数组a，把数组a元素映射到另一个数组的下标上的过程，就是离散化。

此过程中涉及到的两个问题：

1. a中有重复元素怎么办？
    答：**去重**。（不去重在有的题目中并不影响正确性，但一般去重之后，后续的处理效率会比较高
2. 如何算出`a[i]`离散化后的值？
    答：**二分**求得下标即可。（当然，直接遍历也不是不行，但有序数组显然二分比较快

有一个求[区间和](https://www.acwing.com/problem/content/804/)的题，可以比较好的理解这个过程。

解法如下

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, int> PII;

const int N = 300010;

int n, m;
int a[N], s[N];

vector<int> alls;
vector<PII> add, query;

int find(int x)
{
    int l = 0, r = alls.size() - 1;
    while (l < r)
    {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1;
}

vector<int>::iterator unique(vector<int> &a)
{
    int j = 0;
    for (int i = 0; i < a.size(); i ++ )
        if (!i || a[i] != a[i - 1])
            a[j ++ ] = a[i];
    // a[0] ~ a[j - 1] 所有a中不重复的数

    return a.begin() + j;
}

int main()
{
    cin >> n >> m;
    for (int i = 0; i < n; i ++ )
    {
        int x, c;
        cin >> x >> c;
        add.push_back({x, c});

        alls.push_back(x);
    }

    for (int i = 0; i < m; i ++ )
    {
        int l, r;
        cin >> l >> r;
        query.push_back({l, r});

        alls.push_back(l);
        alls.push_back(r);
    }

    // 去重
    sort(alls.begin(), alls.end());
    alls.erase(unique(alls), alls.end());

    // 处理插入
    for (auto item : add)
    {
        int x = find(item.first);
        a[x] += item.second;
    }

    // 预处理前缀和
    for (int i = 1; i <= alls.size(); i ++ ) s[i] = s[i - 1] + a[i];

    // 处理询问
    for (auto item : query)
    {
        int l = find(item.first), r = find(item.second);
        cout << s[r] - s[l - 1] << endl;
    }

    return 0;
}
```

上面的解法中的去重涉及了一些stl的操作，这是C++中去重的常用写法，推荐记住。`unique`函数将把容器内不同的元素排到容器前，并返回容器内这些排完之后的不同元素序列的超尾迭代器。

也可以自行实现如下：

```cpp
vector<int>::iterator unique(vector<int> &a)
{
    int j = 0;
    for (int i = 0; i < a.size(); i ++ )
        if (!i || a[i] != a[i - 1])
            a[j ++ ] = a[i];
    // a[0] ~ a[j - 1] 所有a中不重复的数

    return a.begin() + j;
}
```

题解中的`find`函数可以复习到二分查找，离散化过后的解题方法又可以复习到前缀和，如果要手写`unique`函数的话，也可以涉及到双指针算法，可以说是一道比较好的入门综合题。

## 区间合并

区间合并理解起来很简单，一言以蔽之，就是给我们一堆区间，让我们把其中有交集的合并成一个大的区间。

方法也十分直白：将所给的区间按照左端点排序，依次维护区间。观察下一个区间与当前区间的关系，关系有三种：

1. 含于当前维护的区间，这种情况下不操作；
2. 右端点在当前维护区间外，左端点在当前维护区间内，有交集。这种情况下更新维护区间的右端点为下一个区间的右端点；
3. 下一个区间的左端点在当前维护区间外，无交集；由于预先的排序，这种情况下已经可以将当前维护区间加入答案。

*注：由于有预先排序，因此不会出现下一个区间包含当前维护区间的情况，或者右端点在当前维护区间内而左端点在外的情况。*

Acwing上的[区间合并](https://www.acwing.com/problem/content/805/)题目。

题解如下：

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, int> PII;

void merge(vector<PII> &segs)
{
    vector<PII> res;

    sort(segs.begin(), segs.end());

    int st = -2e9, ed = -2e9;
    for (auto seg : segs)
        if (ed < seg.first)
        {
            if (st != -2e9) res.push_back({st, ed});
            st = seg.first, ed = seg.second;
        }
        else ed = max(ed, seg.second);

    if (st != -2e9) res.push_back({st, ed});

    segs = res;
}

int main()
{
    int n;
    scanf("%d", &n);

    vector<PII> segs;
    for (int i = 0; i < n; i ++ )
    {
        int l, r;
        scanf("%d%d", &l, &r);
        segs.push_back({l, r});
    }

    merge(segs);

    cout << segs.size() << endl;

    return 0;
}
```

上面是y总的写法，但我觉得设置初始维护区间为第一个区间会在思维上更直观一点，下面是我的代码：

```cpp
#include <iostream>
#include <cstdio>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, int> PII;

void merge(vector<PII>& segs) {
    if (segs.empty()) {
        return;
    }

    vector<PII> res;

    sort(segs.begin(), segs.end());

    int st = segs.front().first, ed = segs.front().second;

    for (int i = 1; i < segs.size(); ++ i ) {
        if (ed < segs[i].first) {
            res.push_back({st, ed});
            st = segs[i].first, ed = segs[i].second;
        }
        else {
            ed = max(ed, segs[i].second);
        }
    }

    res.push_back({st, ed});
    segs = res;
}

int main() {
    vector<PII> segs;
    int n;
    cin >> n;
    for (int i = 0; i < n; ++ i ) {
        int l, r;
        cin >> l >> r;

        segs.push_back({l, r});
    }

    merge(segs);

    cout << segs.size() << endl;

    return 0;
}
```

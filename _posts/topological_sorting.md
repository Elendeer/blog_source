---
title: 拓扑排序
date: 2020-08-11 10:38:26
tags: algorithm
---

本文讨论拓扑排序。

<!--more-->

我们从[题目](https://www.acwing.com/problem/content/850/)着手。

## 概念

> 拓扑排序，是对一个有向无环图(Directed Acyclic Graph简称DAG)G进行拓扑排序，是将G中所有顶点排成一个线性序列，使得图中任意一对顶点u和v，若边(u,v)∈E(G)，则u在线性序列中出现在v之前。通常，这样的线性序列称为满足拓扑次序(Topological Order)的序列，简称拓扑序列。简单的说，由某个集合上的一个偏序得到该集合上的一个全序，这个操作称之为拓扑排序。  ——百度百科

注意：只有有向图存在拓扑序列（即拓扑次序）而无向图没有，且可以证明，有向无环图的拓扑序列一定存在。因此*有向无环图*也被称为*拓扑图*。**且，一个有向无环图至少存在一个入度为0的点**。

易知，一个有向无环图删去某个节点之后依然是有向无环图，因此我们的思路可以像这样：

```note
queue 中放入所有入度为0的点
while queue 不为空 {
    t = 队头
    枚举所有t的出边t -> j
        删掉t -> j , d[j] --; //d[j]为j节点的入度
        if (d[j] == 0 )
            j放入queue中
}
```

题解：

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1e5 + 10;
int h[N], e[N], ne[N], idx;
int q[N], d[N];
int n, m;

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ;
}

bool toposort() {
    int hh = 0, tt = -1;
    for (int i = 1; i <= n; ++ i ) {
        if (!d[i]) {
            q[++ tt] = i;
        }
    }

    while (hh <= tt) {
        int t = q[hh ++ ];

        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            -- d[j];
            if (!d[j]) q[++ tt ] = j;
        }
    }

    return tt == n - 1;
}

int main() {
    memset(h, -1, sizeof h ) ;
    cin >> n >> m;
    int a, b;
    for (int i = 0; i < m; ++ i ) {
        cin >> a >> b;
        add(a, b);
        ++ d[b];
    }

    if (toposort()) {
        for (int i = 0; i < n; ++ i ) {
            cout << q[i] << " ";
        }
        puts("");
    }
    else {
        puts("-1");
    }

    return 0;
}
```

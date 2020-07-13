---
title: high_accuracy
date: 2020-07-12 20:24:03
tags: algorithm
---

这段时间噬月在进行算法的复习和学习！

这篇博客将包括高精度算法。

<!--more-->

本篇算法模板来自AcWing的y总。

## 高精度算法

题目可能出现的需要高精的情况：

$A + B$
$A - B$
$A \times a$
$A \div a$
$A \times B$
$A \div B$

其中$len(A), len(B) \le 10^6, a \le 10^6$

A，B属于**大整数**。

---

1. 大整数的存储：

    当我们有一个大整数123456789，我们要如何存它？

    答：使用数组从低到高位存，亦即是：

    数组下标 | 数位
    ---------|----------
    0 | 个位
    1 | 十位
    ... | ...
    相对低位 | 相对低位
    ... | ...
    相对高位 | 相对高位

    这是因为在进行运算的时候可能进位，对数组来说，在后面插入新数显然比在前面插入要方便得多。

2. 运算：

    用代码模拟竖式运算。

---

### 模板

#### 高精度加法

```cpp
// C = A + B, A >= 0, B >= 0
vector<int> add(vector<int> &A, vector<int> &B)
{
    if (A.size() < B.size()) return add(B, A);

    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i ++ )
    {
        t += A[i];
        if (i < B.size()) t += B[i];
        C.push_back(t % 10);
        t /= 10;
    }

    if (t) C.push_back(t);
    return C;
}
```

[高精加法题](https://www.acwing.com/problem/content/793/)

解：

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int> add(vector<int>& A, vector<int>& B) {
    vector<int> C;
    int t = 0;
    for (int j = 0; j < A.size() || j < B.size(); ++ j ) {
        if (j < A.size()) t += A[j];
        if (j < B.size()) t += B[j];

        C.push_back(t % 10);
        t /= 10;
    }

    if (t) C.push_back(t);
    return C;
}

int main() {
    string a, b;
    cin >> a >> b;
    vector<int> A, B;
    for (int j = a.size() - 1; j >= 0; -- j ) A.push_back(a[j] - '0');
    for (int j = b.size() - 1; j >= 0; -- j ) B.push_back(b[j] - '0');

    auto C = add(A, B);

    for (int i = C.size() - 1; i >= 0; -- i ) cout << C[i];
    cout << endl;

    return 0;
}

```

*注：中规中矩的处理，注意存vector的时候低位在前高位在后，遍历的时候不要搞反了即可。但有一个比较重要的理解点是：t在循环刚刚出现时，角色由进位值转变成了临时运算值，而在运算结果被push进vector之后，角色又转变为进位值了。*

---

#### 高精度减法

```cpp
// C = A - B, 满足A >= B, A >= 0, B >= 0
vector<int> sub(vector<int> &A, vector<int> &B)
{
    vector<int> C;
    for (int i = 0, t = 0; i < A.size(); i ++ )
    {
        t = A[i] - t;
        if (i < B.size()) t -= B[i];
        C.push_back((t + 10) % 10);
        if (t < 0) t = 1;
        else t = 0;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}
```

[高精减法题](https://www.acwing.com/problem/content/794/)

解：

```cpp
#include <iostream>
#include <vector>

using namespace std;

bool cmp(vector<int>& A, vector<int>& B) {
    if (A.size() != B.size()) {
        return A.size() > B.size();
    }
    else {
        for (int i = A.size() - 1; i >= 0; -- i ) {
            if (A[i] != B[i]) {
                return A[i] > B[i];
            }
        }
        return true;
    }
}

vector<int> sub(vector<int>& A, vector<int>& B) {
    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); ++ i ) {
        t = A[i] - t;
        if (i < B.size()) {
            t -= B[i];
        }
        C.push_back((t + 10) % 10);

        if (t < 0) {
            t = 1;
        }
        else {
            t = 0;
        }
    }

    while (C.size() > 1 && C.back() == 0) {
        C.pop_back();
    }
    return C;
}

int main() {
    string a, b;
    cin >> a >> b;
    vector<int> A, B;
    for (int i = a.size() - 1; i >= 0; -- i ) A.push_back(a[i] - '0');
    for (int i = b.size() - 1; i >= 0; -- i ) B.push_back(b[i] - '0');

    vector<int> C;
    if (cmp(A, B)) {
        C = sub(A, B);
    }
    else {
        C = sub(B, A);
        cout << "-";
    }

    for (int i = C.size() - 1; i >= 0; -- i ) {
        cout << C[i];
    }
    cout << endl;

    return 0;
}

```

*注：cmp函数是由高位到低位遍历的，写的时候错过，以后记得带上脑子。*

---

#### 高精度乘法

```cpp
// C = A * b, A >= 0, b > 0
vector<int> mul(vector<int> &A, int b)
{
    vector<int> C;

    int t = 0;
    for (int i = 0; i < A.size() || t; i ++ )
    {
        if (i < A.size()) t += A[i] * b;
        C.push_back(t % 10);
        t /= 10;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}
```

[高精乘法题](https://www.acwing.com/problem/content/795/)

解：

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int> mul(vector<int>& A, int b) {
    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size() || t; ++ i ) {
        if (i < A.size()) t += A[i] * b;
        C.push_back(t % 10);
        t /= 10;
    }

    while (C.size() > 1 && C.back() == 0) {
        C.pop_back();
    }
    return C;
}

int main() {
    string a;
    int b;
    cin >> a >> b;

    vector<int> A;
    for (int i = a.size() - 1; i >= 0; -- i ) {
        A.push_back(a[i] - '0');
    }

    vector<int> C = mul(A, b);

    for (int i = C.size() - 1; i >= 0; -- i ) {
        cout << C[i];
    }
    cout << endl;

    return 0;
}
```

*注：高精乘法也比较简单，和高精加法基本一致，不过需要处理可能未处理完的进位。此处的题解将处理这个问题的循环和逐位计算乘法的循环写在了一起。*

---

#### 高精度除法

```cpp
// A / b = C ... r, A >= 0, b > 0
vector<int> div(vector<int> &A, int b, int &r)
{
    vector<int> C;
    r = 0;
    for (int i = A.size() - 1; i >= 0; i -- )
    {
        r = r * 10 + A[i];
        C.push_back(r / b);
        r %= b;
    }
    reverse(C.begin(), C.end());
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}
```

[高精除法题](https://www.acwing.com/problem/content/796/)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> div(vector<int>& A, int b, int& r) {
    vector<int> C;
    r = 0;
    for (int i = A.size() - 1; i >= 0; -- i ) {
        r = r * 10 + A[i];
        C.push_back(r / b);
        r %= b;
    }

    reverse(C.begin(), C.end());

    while (C.size() > 1 && C.back() == 0) {
        C.pop_back();
    }

    return C;
}

int main() {
    string a;
    int b;
    cin >> a >> b;
    vector<int> A;
    for (int i = a.size() - 1; i >= 0; -- i ) {
        A.push_back(a[i] - '0');
    }

    int r;
    vector<int> C = div(A, b, r);

    for (int i = C.size() - 1; i >= 0; -- i ) {
        cout << C[i];
    }
    cout << endl << r << endl;


    return 0;
}
```

*注：高精除法比较特殊，从高位到低位进行运算，因此vector里一开始是高位在前低位在后的。但为了统一数据存储样式，需要把vector倒腾一下。去掉前导零就不说了，传统艺能。这个题解也有一个小技巧，把余数用引用传了回来，属于个人觉得应该属于解题讨巧的一种写法。*

之后会考虑把高精度算法写成大整数类。

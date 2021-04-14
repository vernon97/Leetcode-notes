<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-18 21:14:09
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-02-01 22:37:33
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week28.md
-->
# Week 28 - Leetcode 271 - 280

#### 274 - H指数

从大到小排序， 从右往左 找到第一个满足`f[i] >= i` 的位置即为答案

```cpp
class Solution {
public:
    int hIndex(vector<int>& citations) {
        sort(citations.begin(), citations.end(), greater<int>());
        // 6 5 3 1 0 满足f[i] >= i 
        for(int i = citations.size() - 1; i >= 0; i--)
            if(citations[i] >= i + 1) return i + 1;
        return 0;
    }
};
```

#### 275 - H指数II

> 你可以优化你的算法到对数时间复杂度吗？

一看就是二分跑不掉了

```cpp
class Solution {
public:
    int hIndex(vector<int>& citations) {
        // 这题已经保证有序了 但是是从小到大的有序
        int n = citations.size();
        if(n == 0) return 0;
        reverse(citations.begin(), citations.end());
        int l = 0, r = n - 1;
        while(l < r)
        {
            int mid = l + r + 1 >> 1;
            if(citations[mid] >= mid + 1) l = mid;
            else r = mid - 1;
        }
        if(citations[l] >= l + 1) return l + 1;
        else return l;
    }
};
```

但这里是可以通过处理二分的条件实现不用reverse的 -> 手动的坐标变换

这里的mid 是一定取不到0的

```cpp
class Solution {
public:
    int hIndex(vector<int>& citations) {
        // 这题已经保证有序了 但是是从小到大的有序
        int n = citations.size();
        if(n == 0) return 0;
        //reverse(citations.begin(), citations.end());
        int l = 0, r = n;
        while(l < r)
        {
            int mid = l + r + 1 >> 1;
            if(citations[n - mid] >= mid) l = mid;
            else r = mid - 1;
        }
        return l;
    }
};
```

#### 278 - 第一个错误的版本

纯纯二分法了

```cpp
class Solution {
public:
    int firstBadVersion(int n) {
        // 二分
        int l = 1, r = n;
        while(l < r)
        {
            int mid = l + (r - l) / 2;
            if(isBadVersion(mid)) r = mid;
            else l = mid + 1;
        } 
        return r;
    }
};
```

#### 279 - 完全平方数

这个可以用dp来做了 (完全背包) 首先打表然后状态转移就可 - `o(n * sqrt(n))`

```cpp
class Solution {
public:
    int numSquares(int n) {
        vector<int> squares;
        // 打表预处理平方数 最大到100 * 100
        for(int i = 1; i <= 100; i++)
            squares.push_back(i * i);
        vector<int> f(n + 1, 10000);
        f[0] = 0;
        for(int i = 1; i <= n; i++)
            for(int j = 0; squares[j] <= i; j++)
                f[i] = min(f[i], f[i - squares[j]] + 1);
        return f[n];
    }
};
```

但除了这个dp之外 还有一些数学定理来帮助解决这个问题 （看一看就好了）

**1. 拉格朗日四平方和定理**

states that every natural number can be represented as the sum of four integer squares. That is, the squares form an additive basis of order four.

**2. 勒让德三平方和定理**

Legendre's three-square theorem states that a natural number can be represented as the sum of three squares of integers
if and only if n is not of the form 
`n = 4^a(8b + 7)` for nonnegative integers a and b.

```cpp
class Solution {
public:
    bool check(int x)
    {
        int r = sqrt(x);
        return r * r == x;
    }
    int numSquares(int n) {
        // 判断 答案 1
        if(check(n)) return 1;
        // 判断 答案 2
        for(int i = 1; i <= n / i; i++)
            if(check(n - i * i)) return 2;
        // 判断能否写成 4 ^ a (8b + 7)的形式
        while(n % 4 == 0)
            n /= 4;
        if(n % 8 != 7) return 3;
        return 4;
    }
};
```
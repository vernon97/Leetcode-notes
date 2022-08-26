<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-06-14 00:05:39
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2022-01-15 17:01:41
 * @FilePath: /Leetcode-notes/notes/week37.md
-->
# Week 37 - Leetcode 361 - 370

### 363 - 矩形区域不超过 K 的最大数值和

这题说是hard，但是没能逃过枚举端点；

这里是通过枚举三个端点和一次二分查找来实现找到最接近k的差值，用到的就是二维前缀和

```cpp
class Solution {
public:
    vector<vector<int>> s;
public:
    int get(int x1, int y1, int x2, int y2)
    {
        return s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1];
    }

    int maxSumSubmatrix(vector<vector<int>>& matrix, int k) {
        // 枚举三个边界 更新最大值
        int n = matrix.size(), m = matrix[0].size();
        s = vector<vector<int>>(n + 1, vector<int>(m + 1));
        // 1. 构建二维前缀和
        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= m; j++)
                s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + matrix[i - 1][j - 1];
        // 2. 枚举三个端点 二分找最优解
        int res = -1e9;
        for(int x1 = 1; x1 <= n; x1++)
            for(int x2 = x1; x2 <= n; x2++)
            {
                set<int> sset;
                sset.insert(0); 
                // 遍历从0到y2 - 1
                for(int y2 = 1; y2 <= m; y2++)
                {
                    int si = get(x1, 1, x2, y2);
                    // si - sj <= k -> sj >= si - k
                    auto it = sset.lower_bound(si - k);
                    if(it != sset.end())
                        res = max(res, si - *it);
                    sset.insert(si);
                }
            }
        return res;
    }
};
```

### 365 - 水壶问题

这个题还是狠经典的，有这么几个点：

1. 两个水壶不可能同时半满不满, 必然有一个水壶是满的or空的
2. 对一个不满不空的水壶，倒掉或加满都是没有意义的，等同于初始状态
3. 所以，我们可以认为，对两个水壶的整体来看，每次操作要么就是倒空某个满的水壶，要么就是装满，要么就是互相倒；
4. 对总水量的影响只有+-a 和 +-b 这四种

所以，最终求的就是`ax+by = c` 在 `x + y <= c`的前提下是否有解

**裴蜀定理**：`ax+by=c`有解当且仅当`c`可以被`gcd(a, b)`整除

再复习一下欧几里得算法和扩展欧几里得算法怎么写：

```cpp
int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;
}

int exgcd(int a, int b, int& x, int& y)
{
    if(!b)
    {
        x = 1, y = 0;
        return a;
    }
    int r = exgcd(b, a % b, y, x);
    y -= (a / b) * x;
    return r;
}
```

所以这个题就是判断一下是否`c` 可以被`gcd(a, b)` 整除即可，别忘了 `x + y <= c`这个额外的限制

```cpp
class Solution {
public:
    int gcd(int a, int b)
    {
        return b ? gcd(b, a % b) : a;
    }
    bool canMeasureWater(int jug1Capacity, int jug2Capacity, int targetCapacity) {
        // 好像是扩展欧几里得算法
        // ax + by = c 是否有解
        if(jug1Capacity + jug2Capacity < targetCapacity) return false;
        return targetCapacity % gcd(jug1Capacity, jug2Capacity) == 0;
    }
};
```

### 367 - 有效的完全平方数

不让用sqrt 就是简单的二分查找 找平方根即可

这里不用担心溢出的问题

```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        long long l = 1, r = num;
        while(l < r)
        {
            long long mid = l + r >> 1;
            if(mid * mid >= num) r = mid;
            else l = mid + 1; 
        }
        return r * r == num;
    }
};
```

### 368 - 最大整除子集


整除是有传递性的，排序后只要保证相邻元素是可整除的即可

然后仿照最大上升子序列问题的dp解决方式来求

```cpp
class Solution {
public:
    vector<int> largestDivisibleSubset(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        vector<int> f(n + 1, 1), g(n + 1, -1);
        int maxl = 0, ei = 0;
        for(int i = 0; i < n; i++)
        {
            for(int j = 0; j < i; j++)
                if(nums[i] % nums[j] == 0)
                    if(f[i] < f[j] + 1)
                    {
                        f[i] = f[j] + 1;
                        g[i] = j;
                    }
            if(f[i] > maxl) maxl = f[i], ei = i;
        }
        vector<int> res;
        for(int i = ei; i >= 0; )
        {
            res.push_back(nums[i]);
            i = g[i];
        }
        return res;
    }
};
```


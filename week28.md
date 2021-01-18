<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-18 21:14:09
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-18 21:58:08
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
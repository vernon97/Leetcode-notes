<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-18 20:43:56
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-18 21:13:33
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week27.md
-->
# Week 27 - Leetcode 261 - 270

#### Leetcode 263 - 丑数

丑数系列这个是做过很多了 

这个题就是把所有2 3 5 的质因子除干净 看余数是不是1

```cpp
class Solution {
public:
    bool isUgly(int x) {
        if(x <= 0) return false;
        // 只包含 质因数 2 3 5 的数字 -> 除干净
        while(x % 2 == 0) x /= 2;
        while(x % 3 == 0) x /= 3;
        while(x % 5 == 0) x /= 5;
        return x == 1;
    }
};
```

#### Leetcode 264 - 丑数II

这个题 有点三路归并的意思在里面 

`S2 : 1 * 2, 2 * 2, 3 * 2, ...`

`S3 : 1 * 3, 2 * 3, 3 * 3, ...`

`S5 : 1 * 5, 2 * 5, 3 * 5, ...`

实际上就是这三个序列的三路归并，用p q r 三个指针分别指向 三个序列 并 归并 去重就好了；

```cpp
class Solution {
public:
    int nthUglyNumber(int n) {
        vector<int> ug;
        ug.push_back(1);
        int i = 0, j = 0, k = 0;
        // * 第一个丑数是1 所以只用遍历n - 1次 
        while(--n)
        {
            int p = min(min(ug[i] * 2, ug[j] * 3), ug[k] * 5);
            ug.push_back(p);
            if(p % 2 == 0) i++;
            if(p % 3 == 0) j++;
            if(p % 5 == 0) k++;
        }
        return ug.back();
    }
};
```

#### 268 - 丢失的数字

`[0, n]` 所有数的和 `(n + 0) * (n + 1) / 2` 

遍历一遍数组 从所有数的和中减去 剩下的就是缺少的元素了

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int n = nums.size();
        int res = n * (n + 1) / 2;
        for(int x : nums)
            res -= x;
        return res;
    }
};
```
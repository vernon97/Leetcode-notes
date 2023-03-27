# Week 44 - Leetcode 441 - 450

### 441 - 排列硬币

实际上是解方程，直接二元一次方程组 或者二分下都可以

```cpp
class Solution {
public:
    int arrangeCoins(int n) {
        // 等差数列求和哈，1 + 2 + 3 .. + i = i * (i + 1) / 2 <= n
        // i * i + i <= 2n 
        // 另外也可以二分，这是满足单调性的
        unsigned long long l = 1, r = (1 << 16);
        while(l < r){
            long long mid = l + r + 1 >> 1;
            if(mid * (mid + 1) / 2 <= n) l = mid;
            else r = mid - 1;
        }
        return l;

    }
};
```

### 442 - 数组中重复的数据


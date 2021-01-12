<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-12 22:12:37
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-12 22:25:17
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week24.md
-->

# Week 24 - Leetcode 231 - 240

#### 231 - 2的幂

是2的幂次 就证明 这个数的二进制位只能有一个1;

lowbit运算: 返回保留最后一个1的二进制数 e.g. lowbit(1010) -> 10

其实就是 `x & (-x) == x`

```cpp
class Solution {
public:
    bool isPowerOfTwo(int n) {
        // 这题 2的幂次 证明 二进制只能有一个1
        if(n <= 0) return false;
        return (n & (-n)) == n;
    }
};
```

#### 232 - 用栈实现队列
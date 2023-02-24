# Week 43 - Leetcode 421 - 430

### 412 - 数组中两个数的最大异或值

很经典的一道题先想做法再优化

方法：

- 从前到后枚举每个元素，考虑前i - 1个中哪个数异或值最大

优化：

- 如何找到？ -> trie树

复习一下trie树的建树和查询，对于本题就是查询对应位的反是否存在，然后注意下只有一个元素的边界情况即可；

```cpp
class Solution {
public:
    static const int N = 2e5 * 32 + 10;
    int son[N][2]; // trie树 只有两种
    int idx = 0;
public:
    void insert(int val) {
        int p = 0;
        for(int i = 30; i >= 0; i--) {
            int x = val >> i & 1;
            if(!son[p][x]) son[p][x] = ++idx;
            p = son[p][x];
        }
    }
    int query(int val) {
        int p = 0, res = 0;
        for(int i = 30; i >= 0; i--) {
            int x = val >> i & 1;
            if(son[p][!x]) res = res * 2 + !x, p = son[p][!x];
            else if(son[p][x]) res = res * 2 + x, p = son[p][x];
            else return 0;
        }
        return val ^ res;
    }
    int findMaximumXOR(vector<int>& nums) {
        int res = 0;
        for(int x : nums) {
            res = max(res, query(x));
            insert(x);
        }
        return res;
    }
};
```

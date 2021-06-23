<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-06-17 00:32:32
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-06-21 21:38:39
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week38.md
-->
# Week 38 - Leetcode 371 - 380

### 372 - 超级次方

提到次方就来狠狠回顾一下快速幂怎么写

```cpp
int qmi(int m, int k, int p)
{
    int res = 1, t = m % p;
    while(k)
    {
        if(k & 1) res = res * t % p;
        t = t * t % p;
        k >>= 1;
    }
    return res;
}
```

这个题把快速幂和高精度结合在一起，对于本题而言， 把数字拆成个位和其他，递归求解

` a ^ b3b2b1 % mod = (a ^ b3b2 % mod) ^ 10 * a ^ b1 % mod;`


```cpp
class Solution {
public:
    static const int mod = 1337;
    int qmi(int m, int k, int p)
    {
        int res = 1, t = m % p;
        while(k)
        {
            if(k & 1) res = res * t % p;
            t = t * t % p;
            k >>= 1;
        }
        return res;
    }
    int superPow(int a, vector<int>& b) {
        // 把这个问题转化
        // a ^ b3b2b1 % mod = (a ^ b3b2 % mod) ^ 10 * a ^ b1 % mod;
        if(b.empty()) return 1;
        int last_num = b.back();
        b.pop_back();
        return qmi(superPow(a, b), 10, mod) * qmi(a, last_num, mod) % mod; 
    }
};
```

### 373 - 查找和最小的k组数字

考察的是多路归并

```cpp
class Solution {
public:
    struct Node{
        int sum;
        int aidx;
        int bidx;
        Node(){}
        Node(int _sum, int _aidx, int _bidx) : sum(_sum), aidx(_aidx), bidx(_bidx){}
    };
    struct cmp
    {
        bool operator()(const Node& a, const Node& b) const {
            return a.sum > b.sum;
        }
    };
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        // 这应该是归并排序的一种吧
        vector<vector<int>> res;
        priority_queue<Node, vector<Node>, cmp> heap;
        // 1. 初始化heapheap
        for(int i = 0; i < nums2.size(); i++)
            heap.push({nums1[0] + nums2[i], 0,  i});
        for(int i = 0; i < k && heap.size(); i++)
        {
            auto t = heap.top();
            heap.pop();
            res.push_back({nums1[t.aidx], nums2[t.bidx]});
            if(t.aidx + 1 < nums1.size())
            {
                int naidx = t.aidx + 1, nbidx = t.bidx;
                heap.emplace(nums1[naidx] + nums2[nbidx], naidx, nbidx);
            }
        }
        return res;
    }
};
```

### 374 - 猜数字大小

靠一些二分模板。。。

```cpp
class Solution {
public:
    int guessNumber(int n) {
        int l = 1, r = n;
        while(l < r)
        {
            int mid = (long long)l + r >> 1;
            int res = guess(mid);
            if(res <= 0) r = mid;
            else l = mid + 1;
        }
        return r;
    }
};
```
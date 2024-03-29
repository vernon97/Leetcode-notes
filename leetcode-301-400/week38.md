<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-06-17 00:32:32
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2022-01-16 17:11:31
 * @FilePath: /Leetcode-notes/notes/week38.md
-->
# Week 38 - Leetcode 371 - 380

### 371 - 两整数之和

题里说的是不用加法完成加法运算，这里考的是位运算的应用

- 用**异或**来算不带进位的和(异或：0⊕0=0，1⊕0=1，0⊕1=1，1⊕1=0)
- 用**与** 和 **左移** 来算进位
- 递归计算

举例来说，a=101, b=011

- 第一步: 异或，计算无需进位的部分，a^b=110，最右位的和2需要进位；

- 第二步: 与，a&b=001，结果表示的是需要进位的地方，将它左移一位得到010

第三步，递归调用，求进位前后的和，直到无需进位

```cpp
class Solution {
public:
    int getSum(int a, int b) {
        if(!a) return b;
        int sum = a ^ b, carry = (unsigned)(a & b) << 1;
        return getSum(carry, sum);
    }
};
```

其中负数在计算机中存储是以补码的形式，左移有溢出可能，比如负数加负数（符号位都为1），用unsigned强转，在左移超过int位数之后自动丢弃，**不会溢出**


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

### 376 - 摆动序列

本题是贪心算法，思路还挺难想的；

最优解就是在每个波动的波峰和波谷中选择顶点和底点，与开头结尾两个点组成的序列最长。

证明过程简要来说是反证法，如果存在非顶点或低点被选中的话，这个点的附近一定存在两个比他高or比他低的点，也就是新的局部极值点，与前面的假设矛盾（大概记一下就好了）

代码中的细节有两个，一个是相邻重复元素怎么去除;

`nums.erase(unique(nums.begin(), nums.end()), nums.end())`

原理是unique函数将重复元素交换到后面并返回迭代器，最后直接删除

另一个就是判断上升趋势与下降趋势是否与之前相同的逻辑怎么写 -> **异或运算**，这里值得看一下


```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        // 1. 去除所有相邻重复元素
        nums.erase(unique(nums.begin(), nums.end()), nums.end());
        if(nums.size() == 1) return 1;
        int res = 2;
        bool is_up = nums[0] < nums[1];
        // 2. 找到单调上升与下降区间并计数
        for(int i = 1; i + 1 < nums.size(); i++)
            if((nums[i] < nums[i + 1]) ^ is_up) //出现拐点
            {
                res++;
                is_up = !is_up;
            }
        return res;
    }
};
```

### 377 - 组合总和IV

回忆一下和组合问题三的区别，组合问题三中顺序不同的序列被视为相同的组合，对于这种有限制的组合问题可以用背包问题（这里是完全背包来解决）；

而对于本题，顺序不同的序列被视为不同的组合，这里也是动态规划的思想，具体如下：

- **状态定义：** f[i]表示总和为i的所有排列组合可能性数量
- **状态转移：** 枚举每个元素x 在原有序列后面拼上新的元素x的话，保证和之前所有的序列不重复，所以是f[i] += f[i - x]

```cpp
class Solution {
public:
    vector<unsigned> f;
public:
    int combinationSum4(vector<int>& nums, int target) {
        // f[a]表示总和为a的方案数
        f = vector<unsigned>(target + 1, 0);
        f[0] = 1;
        for(int i = 1; i <= target; i++)
        {
            for(auto x : nums)
            {
                if(x <= i) f[i] += f[i - x]; 
            }
        }
        return f[target];
    }
};
```

### 378 - 有序矩阵中的第K小元素

> 每行元素均按升序排序 -> 多路归并，复杂度n^2logn 

本题有额外的条件，每列元素按升序排序，且复杂度优于n^2 -> nlogn + 第K小元素，可以尝试下二分；

首先判断是否满足二分的性质，对于某个元素t:

- 小于等于t的元素数 < k: 则证明第K小元素 一定存在于[t + 1, matrix[n-1][n-1])
- 小于等于t的元素数 >= k: 第K小元素 index的区间[matrix[0][0], t]

> matrix[n-1][n-1]为最大值，matrix[0][0]为最小值

接下来就是给定一个元素，如何计算小于等于他的元素个数：

对于第一行，可以直接线性扫描得到小于等于他的元素个数；
而对于第二行，**由于按列递增，小于等于他的元素区间一定小于上一行**；

这一性质对于接下来的每一行都成立，区间是越来越小的，我们可以利用这一性质，扫描下一行的时候，直接以上一行的终点向前扫描，直到列扫描结束，或者该行所有元素都满足大于；

因此，找到小于等于他元素个数的时间复杂度是**o(n)**

```cpp
class Solution {
public:
    int n;
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        n = matrix.size();
        int l = matrix[0][0], r = matrix[n-1][n-1] + 1;
        while(l < r)
        {
            int mid = l + r >> 1;
            if(check(matrix, mid, k)) r = mid;
            else l = mid + 1;
        }
        return l;

    }
    int  check(vector<vector<int>>& matrix, int x, int k)
    {
        int end = n - 1, res = 0;
        for(int i = 0; i < n; i++)
        {
            auto& line = matrix[i];
            while(end >= 0 && line[end] > x) end--;
            res += end + 1;
            if(res >= k || end < 0) break;
        }
        // cout << res << ' ' << x << endl;
        return res >= k;
    }
};
```

### 380 - O(1)时间插入，删除和获取随机元素

实现一个数据结构, 可以完成：

- O(1)插入 -> hash
- O(1)删除 -> hash
- O(1)随机返回一个值，保证等概率 -> vector

等概率返回可以通过数组实现，按照下标直接顺序返回就可以了，因此通过哈希+数组的双数据结构来实现，哈希表内存储元素->vector下标，问题是如何实现O(1)的删除；

对于vector pop_back()操作是o(1)的，那么对于想删除元素的话，只需要交换想删除元素 与最后一个就好；

注意下swap和rand()怎么用

```cpp
class RandomizedSet {
public:
    vector<int> arr;
    unordered_map<int, int> h;
public:
    RandomizedSet() {
    }
    
    bool insert(int val) {
        if(h.count(val)) return false;
        else{
            h[val] = static_cast<int>(arr.size());
            arr.push_back(val);
        }
        return true;
    }
    
    bool remove(int x) {
        if(h.count(x)){
            int y = arr.back();
            int px = h[x], py = h[y]; // py 也可以等于 arr.size() - 1, 是一个道理
            swap(arr[px], arr[py]);
            swap(h[x], h[y]);
            h.erase(x);
            arr.pop_back();
            return true;
        }
        return false;

    }
    
    int getRandom() {
        return arr[rand() % arr.size()];
    }
};

```





<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-05-17 13:15:13
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-05-27 14:29:11
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week35.md
-->
# Week 35 - Leetcode 341 - 350

### 341 - 扁平化嵌套列表迭代器

DFS递归，存到一个vector里就好

```cpp
class NestedIterator {
private:
    vector<int> q;
    int k;
public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        k = 0;
        for(NestedInteger& l : nestedList)
        {
            dfs(l);
        }
    }

    void dfs(NestedInteger& l)
    {
        if(l.isInteger()) q.push_back(l.getInteger());
        else {
            vector<NestedInteger> lit = l.getList();
            for(NestedInteger& nl : lit)
                dfs(nl);
        }
    }
    int next() {
        return q[k++];
    }
    
    bool hasNext() {
        return k < q.size();
    }
};
```

### 342 - 4的幂

不能使用循环或递归;

回顾一下：

- 2的幂：二进制表示只能有一个1
- 3的幂：用3^19 判断是否能整除

因此，4^k = 2^2k, 首先保证二进制表示中只能有一个1，且必存在偶数位上

> 如何扣出所有的偶数位？： x & (0xAAAAAAAA)

0xA = 0b1010;

```cpp
class Solution {
public:
    bool isPowerOfFour(int n) {
        // 二进制只能有一个1 且1只能出现在偶数位
        if(n <= 0) return false;
        if((n & (n - 1)) != 0) return false;
        return (n & 0xAAAAAAAA) == 0;
    }
};
```

### 343 - 整数拆分

对于一个数拆成元素的和，使其乘积最大；

是一个经典的小学数奥题目（？），有一个最优策略选择因子；

`X = a_1 + a_2 + ... + a_n`

对于每一个大于等于5的因子`a_i` 都可以拆成 `a_i - 3` 和 `3` 

> `3 * (a_i - 3) > a_i` -> `a_i > 4.5` 即成立

因此 只会包含`2, 3, 4`, 且 `2 + 2 + 2 < 3 + 3` (乘积)

> 所以 尽可能拆成3，2的因子最多只能出现两次

最后，对于x比较小的情况，本题要保证拆成两个元素，特判一下就好

```cpp
class Solution {
public:
    int integerBreak(int n) {
        // 有规律的 
        if(n <= 3) return 1 * (n - 1);
        int p = 1;
        while(n >= 5) n -= 3, p *= 3; // 剩下的2 or 3 or 4 直接乘就可以了
        return p * n;
    }
};
```

### 344 - 反转字符串

标准的双指针 没啥好说的

```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        if(s.size() < 2) return;
        for(int i = 0, j = s.size() - 1; i < j; )
            swap(s[i++], s[j--]);
        return;
    }
};
```

### 345 - 反转字符串中的元音字母

标准的双指针，没啥好说的

```cpp
class Solution {
public:
    string reverseVowels(string s) {
        if(s.size() < 2) return s;
        vector<char> v(s.size());
        v.assign(s.begin(), s.end());
        unordered_set<char> vowels = {'a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'};
        for(int i = 0, j = v.size() - 1; i < j; )
        {
            while(i < j && !vowels.count(v[i]))i++;
            while(i < j && !vowels.count(v[j]))j--;
            if(i < j) swap(v[i++], v[j--]);
        }
        string res;
        res.assign(v.begin(), v.end());
        return res;
    }
};
```

### 347 - 前K个高频元素

前K个高频元素是可以在`O(N)`的时间复杂度下完成的，方法是类似于桶排序的思想：

- 首先统计每个元素的出现次数
- 统计 出现次数 的出现次数
- 从前往后求和，找到出现次数的分界线
- 统计答案即可

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        int n = nums.size();
        unordered_map<int, int> cnt;
        vector<int> s(n + 1);
        // 1. 统计出现次数
        for(auto x : nums) cnt[x]++;
        // 2. 统计出现次数的出现次数
        for(auto [x, c] : cnt) s[c]++;
        // 3. 找到出现次数的分界线
        int i = n, t = 0;
        while(t < k) t += s[i--];
        // 4. 保存出现次数在topk的部分
        vector<int> res;
        for(auto [x, c] : cnt)
            if(c > i) res.push_back(x);
        return res;
    }
};
```

### 349 - 两个数组的交集

哈希表

```cpp

class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        vector<int> res;
        unordered_set<int> m_map_nums1, m_map_nums2;
        m_map_nums1.insert(nums1.begin(), nums1.end());
        for(int x : nums2)
            if(m_map_nums1.count(x) && !m_map_nums2.count(x)) m_map_nums2.insert(x);
        res.assign(m_map_nums2.begin(), m_map_nums2.end());
        return res;
    }
};
```

### 350 - 两个数组的交集II

有重复的可以用multimap 或者 哈希表统计次数都行

```cpp
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        unordered_map<int, int > m_nums1;
        for(auto x : nums1) m_nums1[x]++;
        vector<int> res;
        for(auto x : nums2)
        {
            if(m_nums1[x] > 0)
            {
                res.push_back(x);
                m_nums1[x]--;
            }
        }
        return res;
    }
};
```

**思考题答案：**

如果给定的数组已经排好序呢？你将如何优化你的算法？

> 双指针

如果 nums1 的大小比 nums2 小很多，哪种方法更优？
> 把小的存在哈希表比较

如果 nums2 的元素存储在磁盘上，内存是有限的，并且你不能一次加载所有的元素到内存中，你该怎么办？
> 外部排序：外部归并排序解决内存有限的排序问题 + 双指针

<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-05-07 11:42:23
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-05-17 13:14:45
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week34.md
-->
# Week 34 - Leetcode 331 - 340

### 331 - 验证二叉树的前序序列化

**递归遍历** 是我学不会了 

左右子树必须有东西（`#`也算）所以以这个作为遍历的基础 递归判断 只需从左到右遍历数组一次即可

注意的点：

- 以`,` 作为元素分割的依据，所以要在最后补上一个逗号
- 递归中发现元素遍历完——则存在一遍子树没有元素（结束`#`也没有）所以不合法
- 最后 判断所有元素遍历完才合法 比如`9, #, #, 1`

```cpp
class Solution {
public:
    bool ans = true;
public:
    bool isValidSerialization(string s) {
        int u = 0;
        s +=','; // 以逗号作为element分割依据
        dfs(s, u);
        return ans && u == s.size(); // 注意这里还要保证遍历完
    }
    void dfs(string& s, int& u)
    {
        if(u == s.size())
        {
            ans = false;
            return;
        }
        if(s[u] == '#')
        {
            u += 2;
            return;
        }
        while(s[u] != ',') u++;
        u++; // 过滤等号
        // 根 左 右
        dfs(s, u);
        dfs(s, u);
        return;
    }
};
```

### 332 - 重新安排行程

经典的欧拉回路问题；不重不漏的走所有边；

复习一下欧拉路径和欧拉回路

除了保证连通性之外

欧拉路径：

- 无向图：度数为奇数的点只能有0个or2个 （起点和终点）
- 有向图：所有点的入度=出度 要么除了两个点的入度=出度，且这两个点一个出度比入度大一，一个入度比出度大一（起点和终点）

欧拉回路：

- 无向图：不能有度数为奇数的点
- 有向图：所有点的入度等于出度

求欧拉回路/欧拉路径的方法：

**DFS-在搜完这个点的所有邻接边之后，再将这个点加入答案序列，最终返回序列的逆序**

一些细节：

- 这里是用边来判重，所以**用完了一条边就要删除(额外标记也行）** 
- 无向图是建双向边的形式来实现的 但是用了一条要删除两条边，如何找反向边呢？**双向边都是成对建的，所以`i ^ 1`即为对应的反向边**
- 最小字典序的方法就是**搜的时候按照最小字典序来搜**


```cpp
class Solution {
public:
    unordered_map<string, multiset<string>> h;
    vector<string> res;
public:
    vector<string> findItinerary(vector<vector<string>>& tickets) {
        for(vector<string>& ticket : tickets)
            h[ticket[0]].insert(ticket[1]);
        // 欧拉回路 保证有解所以不用统计入度出度
        string start = "JFK";
        dfs(start);
        reverse(res.begin(), res.end());
        return res;
    }
    void dfs(string& s)
    {
        while(h[s].size())
        {
            string ns = *h[s].begin();
            h[s].erase(h[s].begin());
            dfs(ns);
        }
        res.push_back(s);
    }
};
```

### 334 - 递增的三元子序列

本题是最长上升子序列问题(LIS)问题的一个简化版相当于，找到长度为3的最长上升子序列；

对于LIS问题，有一个贪心的解法可以实现`nlogn`的时间复杂度，具体思路是用一个数组`q[k] 表示长度为K的最长上升子序列中结尾的元素最小值`

这样每次找到`q`中第一个比`nums[i]`大的值，并更新其为nums[i]就行了

这里的思路是把`q`直接用两个数表示 所以可以实现`o(n)`的时间复杂度

```cpp
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        // LIS问题的贪心解法
        if(nums.size() < 3) return false;
        vector<int> q(2, INT_MAX);
        for(int x : nums)
        {
            int k = 2;
            while(k > 0 && q[k - 1] >= x) k--;
            if(k == 2) return true;
            q[k] = x;
        }
        return false;
    }
};
```

### 336 - 回文对

SOS 好多Hard

这题有两个做法 主要记住第二个

1. 暴力+字符串哈希 (o(n^2l))

暴力思想很简单，枚举两个字符串，判断拼成的字串是不是回文子串，这里判断是否回文子串用字符串哈希来实现 不然会TLE

```cpp
class Solution {
public:
    using ULL = unsigned long long;
    static const ULL BASE = 13331;
    vector<ULL> hl, hr, p;
    ULL get(string& s, bool direction)
    {
        ULL res = 0;
        if(direction)
            for(int i = 0; i < s.size(); i++)
                res = res * BASE + s[i] - 'a' + 1;
        else 
            for(int i = s.size() - 1; i >= 0; i--)
                res = res * BASE +  s[i] - 'a' + 1;
        return res;
    }
    vector<vector<int>> palindromePairs(vector<string>& words) {
        int n = words.size(), maxlen = 0;
        hr = vector<ULL>(n + 1, 0);
        hl = vector<ULL>(n + 1, 0);
        for(int i = 0; i < n; i++)
        {
            hl[i] = get(words[i], true);
            hr[i] = get(words[i], false);
            maxlen = max(maxlen, (int)words[i].size());
        }
        // 预处理p
        p = vector<ULL>(maxlen + 1, 0);
        p[0] = 1;
        for(int i = 1; i <= maxlen; i++)
            p[i] = p[i - 1] * BASE;
        
        vector<vector<int>> res;
        for(int i = 0; i < n; i++)
            for(int j = 0; j < n; j++)
            {
                if(i == j) continue;
                ULL hash_left  = hl[i] * p[words[j].size()] + hl[j];
                ULL hash_right = hr[j] * p[words[i].size()] + hr[i];
                if(hash_left == hash_right) res.push_back({i, j});
            }
        return res;
    }
};
```

2. o(nl^2)版本

```cpp
class Solution {
public:
    bool check(string& s)
    {
        for(int i = 0, j = s.size() - 1; i < j; i++, j--)
        {
            if(s[i] != s[j]) return false;
        }
        return true;
    }
    vector<vector<int>> palindromePairs(vector<string>& words) {
        unordered_map<string, int> hash;
        vector<vector<int>> res;
        for(int i = 0; i < words.size(); i++)
        {
            string s = words[i];
            reverse(s.begin(), s.end());
            hash[s] = i;
        }
        for(int i = 0; i < words.size(); i++)
        {
            string& s = words[i];
            // 枚举分割点
            for(int j = 0; j <= s.size(); j++)
            {
                string left  = s.substr(0, j);
                string right = s.substr(j);
                if(hash.count(left) && check(right) && hash[left] != i)  res.push_back({i, hash[left]});
                if(hash.count(right) && check(left) &&hash[right] != i && s.size() != words[hash[right]].size()) res.push_back({hash[right], i});
            }
        }
        return res;
    }
};
```

### 337 - 打家劫舍III

经典的树形DP问题，返回值是选 or 不选 组成的一个pair

```cpp
class Solution {
public:
    using PII = pair<int, int>;
    int rob(TreeNode* root) {
        // 树形dp 返回状态是选 or 不选
        PII res = dfs(root);
        return max(res.first, res.second);
    }
    PII dfs(TreeNode* root)
    {
        if(root == nullptr) return {0, 0};
        PII left = dfs(root->left), right = dfs(root->right);
        // 1. 不选root
        int not_select_root = max(left.first, left.second) + max(right.first, right.second);
        int select_root = left.first + right.first + root->val;
        return {not_select_root, select_root};
    }
};
```

### 338 - 比特位计数

计算从`1 - n` 中, 每个数的二进制表示中1的个数

递推：对于每个数`x`的二进制表示中1的个数，可以通过 `x >> 1` 的个数 加上最后一位是否为1来实现；

```cpp
class Solution {
public:
    vector<int> countBits(int num) {
        vector<int> f(num + 1, 0);
        for(int i = 1; i <= num; i++)
            f[i] = f[i >> 1] + (i & 1);
        return f;
    }
};
```

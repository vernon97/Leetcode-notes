# Week 40 - Leetcode 391 - 400

### 391 - 完美矩形

背一下吧hh 本题的意思大概是所有的小矩形 不重不漏的拼成一个小矩形，本题的解法将矩形的重叠问题拆成了各个顶点的关系，所有小矩形的顶点需要满足以下关系：

- 重叠次数为1的点有且仅有四个 （拼成的四个顶点）
- 重叠次数为3的点不存在，剩下的都是重叠次数2/4的点
- 小矩形的面积和等于拼成的四个顶点的面积

另外还有个小细节在于pair是没有hash函数默认实现的，所以不能直接用unordered_map, 但可以用map来存

```cpp
class Solution {
public:
    using PII = pair<int, int>;
    using LL = long long;
    bool isRectangleCover(vector<vector<int>>& rectangles) {
        map<PII, int> cnt;
        LL sum;
        for(auto& x : rectangles) {
            int a = x[0], b = x[1], c = x[2], d = x[3];
            cnt[{a, b}] ++, cnt[{a, d}] ++, cnt[{c, b}]++, cnt[{c, d}]++;
            sum += static_cast<LL>(c - a) * (d - b);
        }
        int ones = 0;
        vector<vector<int>> res;
        for(auto& [a, b] : cnt) 
        {
            if(b == 1) ones++, res.push_back({a.first, a.second});
            else if (b == 3) return false;
        }
        if(ones != 4) return false;
        sort(res.begin(), res.end());
        return sum == static_cast<LL>(res[3][0] - res[0][0]) * (res[3][1] - res[0][1]);

    }
};
```

### 392 - 判断子序列

比较基础的双指针，注意下判出条件

```cpp
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int i = 0, j = 0;  
        if(s.empty()) return true;
        for(; i < s.size() && j < t.size(); i++,j++) {
            while(j < t.size() && s[i] != t[j]) j++;
            if(j >= t.size()) break;
        }
        return i == s.size();
    }
};
```

### 393 - UTF-8 编码验证

比较基础的位运算题，也可以复习复习常用的位运算操作，比如取位数等等

lowbit运算 返回最后一位1 ：n & (-n)

```cpp
class Solution {
public:
    int countTopOnes(int val) {
        int cnt = 0;
        val = val & (255);
        for(int i = 7; i >= 0; i--) {
            if((val >> i) & 1) cnt++;
            else break;
        }
        return cnt;
    }
    bool validUtf8(vector<int>& data) {
        for(int i = 0; i < data.size(); i++) {
            int cnt = countTopOnes(data[i]);
            if(cnt == 1 || cnt > 4) return false;
            while(--cnt > 0){
                if(i + 1 >= data.size()) return false;
                if(countTopOnes(data[++i]) != 1) return false; 
            }
        }
        return true;
    }
};
```

### 394 - 字符串编码

类似的递归都是传引用index的递归，可以学习一下处理方式

```cpp
class Solution {
public:
    string decodeString(string s) {
        int u = 0;
        return dfs(s, u);
    }
    string mulstr(string s, int cnt) {
        stringstream ss;
        while(cnt--) {
            ss << s;
        }
        return ss.str();
    }
    string dfs(string& s, int& u){
        string res;
        while(u < s.size() && s[u] != ']') {
            if(isalpha(s[u])) res += s[u++];
            else if (isdigit(s[u])) {
                int k = u;
                while(k < s.size() && isdigit(s[k])) k++;
                int cnt = stoi(s.substr(u, k - u));
                u = k + 1;
                string ans = dfs(s, u);
                u++;
                res += mulstr(ans, cnt);
            }
        }
        return res;
    }
};
```

### 395 - 至少有K个重复字符的最长子串

对于满足要求的最短/最长子串，一般来说都是双指针解法，但是使用双指针需要满足单调性，本题K个重复字符的条件显然是不满足的，这里的处理比较巧妙，是通过增加一个条件：子串中最多存在_K种字符，在最多存在_K种字符的最长子串中，一定包含至少有k个重复字符的最长子串的解，这里的处理是比较巧妙的，可以记一下：

另外判断有多少种字母和重复字符的数量都可以通过前缀和来判断，一次预处理可以判断两种；

class Solution {
public:
    vector<vector<int>> f;
public:
    int longestSubstring(string s, int k) {
        // 看起来是dp，还是差分
        // f[i][26] 从0 到 i 每个字母的重复次数
        f = vector<vector<int>>(s.size() + 2, vector<int>(27, 0));
        int res = 0;
        for(int i = 1; i <= s.size(); i++) {
            for(int j = 0; j < 26; j++)
                f[i][j] = f[i - 1][j] + (s[i - 1] - 'a' == j);
        }
        for(int _K = 1; _K <= 26; _K ++) {
            for(int i = 1, j = 1; i <= s.size(); i++) {
                while(j < i && check2(s, j, i, _K)) j++;
                if(check(s, j, i, k)) res = max(res, i - j + 1);
            }
        }
        return res;
    }
    bool check2(string& s, int si, int sj, int k) {
        int cnt = 0;
        for(int i = 0; i < 26; i++) {
            int diff = f[sj][i] - f[si - 1][i];
            if(diff > 0) cnt++; 
        }
        return cnt > k;
    }
    bool check(string& s, int si, int sj, int k) {
        for(int i = 0; i < 26; i++) {
            int diff = f[sj][i] - f[si - 1][i];
            if(diff > 0 && diff < k) return false; 
        }
        return true;
    }
};

### 396 - 旋转函数

算是一个比较直接的DP，从给的公式来看，可以看到
F(k) = 0 * arrk[0] + 1 * arrk[1] + ... + (n - 1) * arrk[n - 1]

那么，F(k) - F(k - 1) = sum(nums) - n * nums[n - k], 这样一维dp可以用一个变量来节省空间，具体代码如下：


```cpp
class Solution {
public:
    int maxRotateFunction(vector<int>& nums) {
        // 看起来就是DP
        // F(i) - F(i - 1) = sum(nums) - (n) * nums[n - i]
        int f = 0, n = nums.size(), sum = 0;
        for(int i = 0; i < n; i++)
            f += i * nums[i], sum += nums[i];
        int res = f;
        for(int i = 1; i < n; i++)
        {
            f = f + sum - n * nums[n - i];
            res = max(res, f);
        }
        return res;
    }
};
```

### 397 - 整数替换

看起来复杂但实际上是个搜索题，记忆化搜索即可

复杂度的证明比较复杂，也没看懂，这里不再赘述了；

```cpp
using LL = long long;
class Solution {
public:
    unordered_map<LL,int> memo; //记忆化

    int integerReplacement(int n) {
        memo[1] = 0; 
       return f(n);
    }
    int f(LL n) {
        if (memo.count(n)) return memo[n];
        if (n % 2 == 0) {
            memo[n] = f(n / 2) + 1;
        } else {
            memo[n] = min(f(n - 1), f(n + 1)) + 1;
        }
        return memo[n];

    }
};
```

### 398 - 随机数索引

用两个hash表来存就好了，另外蓄水池抽样算法简单了解一下：
每次只保留一个数，当遇到第 i 个数时，以 1/i 的概率保留它，i - 1 / i的概率保留原来的数。遍历完之后每个数被选择的概率都是相等的；

```cpp
class Solution {
public:
    unordered_map<int, vector<int>> h;
public:
    Solution(vector<int>& nums) {
        for(int i = 0; i < nums.size(); i++) {
            h[nums[i]].push_back(i);
        }
    }
    
    int pick(int target) {
        int n = h[target].size();
        return h[target][rand() % n];
    }
};
```

### 400 - 第n位数字

是一个找规律的题目，其中比较细致的分拆，可以学习一下；

- 找到第n位所在数字对应的位数cnt
- 找到第n位所在数字
- 找到第n位所在数字对应位数

从第一点开始，一位数一共 9 * 1个，两位数一共 90 * 2个，三位数一共 900 * 3个，以此就可以看n属于那个区间找到对应位数；

这样，第二点，找到所在数字，就可以从位数出发，比如四位数就是1000, 接下来每个数字三位数，n / cnt上取整就可以；

第三点，找到第n位数字对应数字之后，取余数就可以了，这里直接用to_string转字符串来取

找规律的边界问题，需要多举举例子来佐证

```cpp
class Solution {
public:
    int findNthDigit(int n) {
        long long cnt = 1, start = 1, count = 9;
        while(n > count) {
            n -= count;
            cnt++;
            start *= 10;
            count = 9 * start * cnt;
        }
        return to_string(start - 1 + (n + cnt - 1) / cnt)[(n + cnt - 1) % cnt] - '0';
    }
};
```

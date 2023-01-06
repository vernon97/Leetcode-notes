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
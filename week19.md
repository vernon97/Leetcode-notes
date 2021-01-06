<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-06 18:49:04
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-06 20:48:33
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week19.md
-->

# Week 19 - Leetcode 181 - 190

```diff
+ 本周只有四道题（？
```

#### 187 - 重复的DNA序列

感觉这个题 也不是KMP.. 主要是子串要枚举很多次 字符串哈希吧应该是

复习一下KMP tmd 写一次忘一次（暴躁）

```cpp
int n = s.size(), m = p.size();
s = ' ' + s, p = ' ' + p;
vector<int> ne(m + 2);
// 1. 统计ne
for(int i = 2, j = 0; i <= m; i++)
{
    while(j && p[i] != p[j + 1]) j = ne[j];
    if(p[i] == p[j + 1]) j++;
    ne[i] = j;
}
// 2. KMP匹配
for(int i = 1, j = 0; i <= n; i++)
{
    while(j && s[i] != s[j + 1]) j = ne[j];
    if(s[i] == p[j + 1]) j++;
    if(j == m)
    {
        // 匹配成功的逻辑
    }
}
```

这题应该就是字符串哈希了, 这里除了常规的字符串哈希逻辑之外

```cpp
typedef unsigned long long ULL;
class Solution {
public:
    static const ULL M = 13331;
    vector<ULL> h, p;
public:
    ULL get(int l, int r)
    {
        return h[r] - h[l - 1] * p[r - l + 1];
    }
    vector<string> findRepeatedDnaSequences(string s) {
        // 一看就是字符串哈希
        int n = s.size();
        vector<string> res;
        s = ' ' + s;
        h = vector<ULL>(n + 1), p = vector<ULL>(n + 1);
        p[0] = 1;
        for(int i = 1; i <= n; i++)
        {
            h[i] = h[i - 1] * M + s[i] - 'A' + 1;
            p[i] = p[i - 1] * M;
        }
        
        unordered_map<ULL, int> hash;
        // 枚举起点
        for(int k = 1; k <= 10; k++)
        {
            for(int i = k; i + 9 <= n; i += 10)
            {
                ULL temp  = get(i, i + 9);
                hash[temp]++;
                if(hash[temp] > 1)
                {
                    res.push_back(s.substr(i, 10));
                    hash[temp] = -1e9;
                }
            }
        }
        return res;
    }
};
```



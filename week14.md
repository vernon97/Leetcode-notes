<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-12-18 23:49:16
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-12-19 01:17:16
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week14.md
-->
# Week 14 - Leetcode 131 - 140

#### 131 - 分割回文串

这里有一个很重要的思想，预处理减少复杂度；

这里可以用递推的思想把判断回文串的步骤提前判断出来；
从`f[i + 1, j - 1]` 到 `f[i, j]`

明天再写

```cpp
class Solution {
public:
    int n;
    vector<vector<string>> res;
    vector<vector<bool>> f;
    vector<string> path;
public:
    vector<vector<string>> partition(string s) {
        n = s.size();
        f = vector<vector<bool>>(n, vector<bool>(n));

        // 注意这里的枚举顺序
        for(int j = 0; j < n; j++)
            for(int i = 0; i <= j; i++)
            {
                if (i == j)
                    f[i][j] = true;
                else if (s[i] == s[j])
                {
                    if (i + 1 > j - 1 || f[i + 1][j - 1])
                        f[i][j] = true;
                }
            }

        dfs(s, 0);
        return res;
    }
    void dfs(string& s, int u)
    {
        if(u == n) res.push_back(path);
        else
        {
            for(int i = u; i < n; i++)
            {
                if(f[u][i])
                {
                    path.push_back(s.substr(u, i - u + 1));
                    dfs(s, i + 1);
                    path.pop_back();
                }
            }
        }
    }
};
```

#### 132 - 分割回文串ii

明天再写

```cpp
class Solution {
public:
    int minCut(string s) {
        // 1. 预处理回文串判断
        int n = s.size();
        s = ' ' + s;
        vector<vector<bool>> isPara(n + 1, vector<bool>(n + 1));
        for(int j = 1; j <= n; j++)
            for(int i = 1; i <= j; i++)
            {
                if(i == j) isPara[i][j] = true;
                else if (s[i] == s[j])
                {
                    if(i + 1 > j - 1 || isPara[i + 1][j - 1])
                        isPara[i][j] = true;
                }
            }
        // 2. 动态规划 f[i] 指s[1..i]的所有分类情况
        vector<int> f(n + 1, 1e9);
        f[0] = 0;
        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= i; j++)
            {
                if(isPara[j][i])
                    f[i] = min(f[i], f[j - 1] + 1);
            }
        return f[n] - 1;
    }
};
```
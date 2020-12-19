<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-12-18 23:49:16
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-12-19 20:51:35
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week14.md
-->
# Week 14 - Leetcode 131 - 140

#### 131 - 分割回文串

这里有一个很重要的思想，预处理减少复杂度；

这里可以用递推的思想把判断回文串的步骤提前判断出来；
从`f[i, j]` 表示 `s[i ... j]`是回文子串（闭区间）

`f[i, j]` 可以由 `f[i + 1, j - 1]` 递推而来 只要满足 `s[i+1 ... j-1]` 是回文子串并且`s[i] == s[j]`即可

这样再dfs搜索分界线即可。

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
                    f[i][j] = true; // 只有一个字母 
                else if (s[i] == s[j])
                {
                    if (i + 1 > j - 1 || f[i + 1][j - 1]) // 只有两个字母 或者 f[i+1][j-1]为true
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

这里第一步 预处理回文串判断和上面一题是一样的；

接下来的动态规划的状态`f[i]` 表示 `s[1 ... i]`中满足都为回文子串的所有分割方式，值为最小的子块数量；

显然 `f[i]` 可以在所有满足`s[k ... i]`为回文子串的状态中转移过来， `f[i] = min(f[k - 1] + 1)`

最后的答案是`f[n] - 1` 分割次数比子块数量少1；

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

#### 133 - 克隆图


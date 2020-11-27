<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-11-26 01:42:41
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-11-27 15:01:44
 * @FilePath: /Leetcode-notes/week06.md
-->

# Week 06 - Leetcode 51 - 60

#### 51 - N皇后

```diff
+ DFS
```

每一行必定且仅会出现一个皇后，所以按行枚举出现的位置，并用`col[n], dg[n], udg[n]` 记录列， 主对角线， 副对角线是否已经出现过皇后即可；

```cpp
class Solution {
public:
    int n;
    vector<vector<string>> res;
    vector<string> path;
    vector<bool> col, dg, udg;
public:
    vector<vector<string>> solveNQueens(int n) {
        this->n = n;
        // DFS - 回溯 + 剪枝
        // 按行枚举 用bool数组记录 列 主斜线 副斜线
        col = vector<bool>(n);
        dg  = vector<bool>(2 * n);
        udg = vector<bool>(2 * n);
        string p(n, '.');
        path = vector<string>(n, p);
        dfs(0);
        //cout << p << endl;
        return res;
    }
    void dfs(int u)
    {
        if(u == n)
            res.push_back(path);
        else
        {
            for(int i = 0; i < n; i++)
            {
                if(!col[i] && !dg[i + u] && !udg[n + i - u])
                {
                    col[i] = dg[i + u] = udg[n + i - u] = true;
                    path[u][i] = 'Q';
                    dfs(u + 1);
                    col[i] = dg[i + u] = udg[n + i - u] = false;
                    path[u][i] = '.';
                }
            }
        }
    }
};
```

#### 52 - N皇后II

可以说和上面那个题一模一样了；

```cpp
class Solution {
public:
    int n, res;
    vector<string> path;
    vector<bool> col, dg, udg;
public:
    int totalNQueens(int n) {
        this->n = n;
        // DFS - 回溯 + 剪枝
        // 按行枚举 用bool数组记录 列 主斜线 副斜线
        col = vector<bool>(n);
        dg  = vector<bool>(2 * n);
        udg = vector<bool>(2 * n);
        string p(n, '.');
        path = vector<string>(n, p);
        dfs(0);
        //cout << p << endl;
        return res;
    }
    void dfs(int u)
    {
        if(u == n)
            res++;
        else
        {
            for(int i = 0; i < n; i++)
            {
                if(!col[i] && !dg[i + u] && !udg[n + i - u])
                {
                    col[i] = dg[i + u] = udg[n + i - u] = true;
                    path[u][i] = 'Q';
                    dfs(u + 1);
                    col[i] = dg[i + u] = udg[n + i - u] = false;
                    path[u][i] = '.';
                }
            }
        }
    }
};
```

#### 53 - 最大子序和

动态规划 f[i]表示已为结尾的连续子数组的最大和；
`f[i] = max(f[i - 1] + w[i], w[i]);`

仅用到上一个状态 -> 空间优化；

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        //vector<int> f(nums.size() + 1, 0);
        int res = INT_MIN, f = 0;
        for(int i = 1; i <= nums.size(); i++)
        {
            f = max(f + nums[i - 1], nums[i - 1]);
            res = max(res, f);
        }
        return res;
    }
};
```

#### 54 - 螺旋矩阵

```diff
+ 模拟 (偏移量)
```

经典问题了 在各种地方都出现过(x)

![avatar](figs/09.jpeg)

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        int n = matrix.size();
        if(!n) return res;
        int m = matrix[0].size();
        int dx[4] = {0, 1, 0, -1}, dy[4] = {1, 0, -1, 0};
        bool st[n][m];
        memset(st, false, sizeof st);
        for(int i = 0, x = 0, y = 0, d = 0; i < n * m; i++)
        {
            res.push_back(matrix[x][y]);
            st[x][y] = true;
            int a = x + dx[d], b = y + dy[d];
            if(a < 0 || a >= n || b < 0 | b >= m || st[a][b])
            {
                d = (d + 1) % 4;
                a = x + dx[d], b = y + dy[d];
            }
            x = a, y = b;
        }
        return res;
    }
};
```

#### 55 - 跳跃游戏

和第四十五题（跳跃游戏ii） 很像， 代码也差不多 无非是从记录步数 到记录能否跳到的bool值

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        vector<bool> f(nums.size());
        f[0] = true;
        for(int i = 1, j = 0; i < nums.size(); i++)
        {
            while(j + nums[j] < i) j++;
            f[i] = f[i] || f[j];
        }
        return f[nums.size() - 1];
    }
};
```

#### 56 - 合并区间

```diff
+ 双指针算法
```

经典题型了。。 又忘记了复习一下

1. 按照start 给所有区间排序
2. 枚举每个区间 比较最开始的end 和下一个区间的start 是否重叠；
3. 重叠就合并

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        // 双指针模板题 
        // 1. 按照end排序
        vector<vector<int>> res;
        if(intervals.empty()) return res;
        sort(intervals.begin(), intervals.end());
        // 2. 双指针遍历；
        int st = -2e9, ed = -2e9;

        for(vector<int>& interval : intervals)
        {
            if(ed < interval[0])
            {
                if(st != -2e9) res.push_back({st, ed});
                st = interval[0], ed = interval[1];
            }
            else
            {
                ed = max(ed, interval[1]);
            }
        }
        if(st != -2e9) res.push_back({st, ed});
        return res;
    }
};
```

#### 57 - 插入区间

更像是模拟题：找到有交集的那一小段区间合并，剩下的维持原样；

```cpp
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        int st = newInterval[0], ed = newInterval[1], si = -1, ei = -1;
        for(int i = 0; i < intervals.size(); i++)
        {
            if(intervals[i][1] < st)
                continue;
            else
            {
                ei = si = i;
                st = min(st, intervals[i][0]);
                while(ei < intervals.size() && ed >= intervals[ei][0])
                    ed = max(ed, intervals[ei][1]), ei++;
                break;
            }
        }
        // 注意边界问题
        if(si != -1)
        {
            // 有交集的中间直接删掉
            intervals.erase(intervals.begin() + si, intervals.begin() + ei);
            // 插入合并后的新区间
            intervals.insert(intervals.begin() + si, {st, ed});
        }
        else
        {
            // 没找到有交集的 直接插在最后即可
            intervals.push_back(newInterval);
        }
        return intervals;
    }
};
```

#### 58 - 最后一个单词的长度

stringstream 的应用： 把字符串变成流 常用于带空格的字符串拆分；

```cpp
class Solution {
public:
    int lengthOfLastWord(string s) {
        stringstream ss(s);
        string word;
        int res = 0;
        while(ss >> word)
            res = word.size();
        return res;
    }
};
```

实际上 就是找到最后的字符串就可以了 用一个指针去找: 模拟；

```cpp
class Solution {
public:
    int lengthOfLastWord(string s) {
        int i = s.size() - 1, res = 0;
        while(i >= 0)
        {
            if(s[i] == ' ')
            {
                if(res != 0) break;
            } else {
                res++;
            }
            i--;
        }
        return res;
    }
};
```

#### 59 - 螺旋矩阵II

和54 螺旋矩阵 差不多 都是定义偏移量往里写就好了

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> res(n, vector<int>(n, 0));
        int dx[4] = {0, 1, 0, -1}, dy[4] = {1, 0, -1, 0};
        int x = 0, y = 0, d = 0;
        for(int i = 1; i <= n * n; i++)
        {
            int a = x + dx[d], b = y + dy[d];
            if(a < 0 || a >= n || b < 0 || b >= n || res[a][b] != 0)
            {
                d = (d + 1) % 4;
                a = x + dx[d], b = y + dy[d];
            }
            res[x][y] = i;
            x = a, y = b;
        }
        return res;
    }
};
```

<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-06-28 23:17:55
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-06-29 00:42:19
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/codingtests/Leetcode-266周赛.md
-->
### 01 - 字符串中的最大奇数

这题比较基础，找到最靠后的一个奇数输出就可以了

```cpp
class Solution {
public:
    string largestOddNumber(string num) {
        if(num.empty()) return "";
        for(int i = num.size() - 1; i >= 0; i--)
        {
            if((num[i] - '0') & 1) return num.substr(0, i + 1);
        }
        return "";

    }
};
```

### 02 - 你完成的完整对局数

这题有两个很重要学到的点，一个是字符串格式化输入输出`sscanf sprintf`这两个函数怎么用（很常用！）

还有就是上取整下取整别忘了，**这题都转化成分钟来做就可以了**

最后，有可能有负一的情况，记得额外判断一下就就好

```cpp
class Solution {
public:
    int getMinute(string& time)
    {
        int x, y;
        sscanf(time.c_str(), "%d:%d", &x, &y);
        return x * 60 + y;
    }
    int numberOfRounds(string startTime, string finishTime) {
        int startMinute = getMinute(startTime), finishMinute = getMinute(finishTime);
        if(startMinute > finishMinute)
            finishMinute += 24 * 60;
        return max(0, (finishMinute) / 15 - (startMinute + 14) / 15); 
    }
};
```

### 03 - 统计子岛屿

这个题就在正常的dfs上 额外加了一个条件，探索到grid1为0，而grid2为1的时候不计数，但是仍要正常统计完

所以这里的处理就是额外增加了一个flag 去统计有没有上面这种情况发生，没有才统计一次

```cpp
class Solution {
public:
    int n, m;
    int dx[4] = {1, 0, -1, 0}, dy[4] = {0, 1, 0, -1};
public:
    bool dfs(int si, int sj, vector<vector<int>>& grid1, vector<vector<int>>& grid2)
    {
        grid2[si][sj] = 0;
        bool flag = true;
        for(int i = 0; i < 4; i++)
        {
            int x = si + dx[i], y = sj + dy[i];
            if(x < 0 || x >= n || y < 0 || y >= m) continue;
            if(grid2[x][y] == 0) continue;
            if(grid1[x][y] == 0) flag = false;
            if(!dfs(x, y, grid1, grid2)) flag = false;
        }
        return flag;
    }
    int countSubIslands(vector<vector<int>>& grid1, vector<vector<int>>& grid2) {
        n = grid1.size(), m = grid1[0].size();
        int res = 0;
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
                if(grid1[i][j] == 1 && grid2[i][j] == 1)
                    if(dfs(i, j, grid1, grid2)) res++;
            
        return res;
    }
};
```

### 04 - 查询差绝对值的最小值

这个题真的学到了，主要是从数据范围出发，如果没有数据都在`[1, 100]`这个条件是无法摆脱双重循环枚举的，所以是`o(n^2)`

明天写

```cpp
```

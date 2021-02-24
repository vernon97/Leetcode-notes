<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-02-01 22:20:33
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-02-24 17:53:07
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week29.md
-->

# Week 29 - Leetcode 281 - 290

#### 282 - 给表达式添加运算符

给定一个仅包含数字 0-9 的字符串和一个目标值，在数字之间添加二元运算符（不是一元）+、- 或 * ，返回所有能够得到目标值的表达式。

这题显然就是DFS了 搜索每两个数字间可能出现的运算符并组合即可

问题就在于本题的方案如何记录， 这里用到了和线段树类似的运算表达式来简化 我们用一个形如

> `a + b * _` 的式子来表示所有方案 这样

```diff
+ 如果后面是 + : (a + b * c) + 1  * _
+ 如果后面是 - : (a + b * c) + -1 * _
+ 如果后面是 * : a + (b * c) * _
```

这里我们额外规定最后一个数后面可以跟 `+`

```cpp
using LL = long long;
class Solution {
public:
    vector<string> ans;
    string path;
    LL target;
public:
    vector<string> addOperators(string num, int target) {
        path.resize(100);
        this->target = target;
        dfs(num, 0, 0, 0, 1); // 最开始 0 + 1 * _
        return ans;
    }
    void dfs(string num, int u, int len, LL a, LL b)
    {
        if(u == num.size())
        {
            if(a == target) ans.push_back(path.substr(0, len - 1)); // 除去最后一个加号
        }
        else
        {
            LL c = 0;
            for(int i = u; i < num.size(); i++)
            {
                c = c * 10 + num[i] - '0';
                path[len++] = num[i];
                // +
                path[len] = '+';
                dfs(num, i + 1, len + 1, a + b * c, 1);

                if(i + 1 < num.size())
                {
                    // - 
                    path[len] = '-';
                    dfs(num, i + 1, len + 1, a + b * c, -1);
                    // *
                    path[len] = '*';
                    dfs(num, i + 1, len + 1, a, b * c);
                }
                // ! 注意这里前导0的情况
                if(num[u] == '0') break;
            }
        }  
    }
};
```

### 283 - 移动0


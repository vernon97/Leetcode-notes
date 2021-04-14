<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-04-14 20:05:45
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-04-14 22:13:12
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week31.md
-->
# Week 31 - Leetcode 301 - 310

好家伙上来就这么硬核的吗...

### 301 - 删除无效的括号

记得复习一下leetcode 32.最长有效括号

**有效括号序列**要满足的要求：

- 左右括号数量相同
- 任意一个前缀中，左括号数量 >= 右括号数量

**第一步：统计要删掉多少左括号和右括号**

> `l` 表示还剩多少未匹配的左括号 `r`表示多少非法的右括号

那么`l ++`代表当前未匹配的左括号，如果遇到右括号并且当前未匹配的左括号个数大于0，那么`l --`，说明当前右括号前面有与之匹配的左括号，如果未匹配的左括号个数等于0，说明当前这个括号是不合法的，`r ++`

最后`l`代表还没有匹配的左括号个数，`r`表示非法的右括号个数，都是我们需要删除的个数。


**第二步：DFS搜索+剪枝**

这里的剪枝策略有两部分：

- 预处理删除的括号数
- 重复去重 ((( 对于这种连续的括号序列删除那个都是一样的 所以这里枚举的是删除的个数

函数签名`void dfs(string& s, int u, string path, int cnt, int l, int r)`

这里面`cnt`和上面的`l`含义一致，还有多少未匹配的左括号， `l` `r`都是还需要删除的括号数量


```cpp
class Solution {
public:
    vector<string> res;
public:
    vector<string> removeInvalidParentheses(string s) {
        int l = 0, r = 0;
        // 第一步：统计待删除的字符数量
        for(auto x : s)
        {
            if(x == '(') l++;
            else if (x == ')')
            {
                if(l == 0) r++;
                else l--;
            }
        }
        dfs(s, 0, "", 0, l, r);
        return res;
    }
    void dfs(string& s, int u, string path, int cnt, int l, int r)
    {
        if(u == s.size())
        {
            if(!cnt) res.push_back(path); // 合法条件：左右括号数量一致
            return;
        }
        if(s[u] != '(' && s[u] != ')') dfs(s, u + 1, path + s[u], cnt, l, r);
        else if (s[u] == '(')
        {
            int k = u;
            while(k < s.size() && s[k] == '(') k++;
            // 按照数量枚举，先把所有的都删了
            l -= k - u;
            for(int i = k - u; i >= 0; i--)
            {
                if(l >= 0) dfs(s, k, path, cnt, l, r);
                path += '(';
                cnt++, l++; // 
            }
        }
        else if (s[u] == ')')
        {
            int k = u;
            while(k < s.size() && s[k] == ')') k++;
            r -= k - u;
            for(int i = k - u; i >= 0; i--)
            {
                if(cnt >= 0 && r >= 0) dfs(s, k, path, cnt, l, r); // 注意这里的 cnt >= 0
                path += ')';
                cnt--, r++;
            }
        }
    }
};
```

### 303 - 区域和检索 - 数组不可变

这题简单 一维前缀和罢了

```cpp
class NumArray {
public:
    vector<int> s;
public:
    NumArray(vector<int>& nums) {
        int n = nums.size();
        s = vector<int>(n + 1);
        for(int i = 1; i <= n; i++)
        {
            s[i] = s[i - 1] + nums[i - 1];
        }
    }
    
    int sumRange(int left, int right) {
        return s[right + 1] - s[left];
    }
};
```



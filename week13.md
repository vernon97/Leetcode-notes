<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-12-15 23:30:24
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-12-18 16:53:40
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week13.md
-->
# Week 13 - Leetcode 121 - 130

#### 121 - 买卖股票的最佳时机

记录从 `1 ~ i - 1` 的价值最小值买入， 当前卖出的价值，维护其最大值即可；

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int minv = 2e9, res = 0;
        for(auto p : prices)
        {
            res = max(res, p - minv);
            minv = min(minv, p);
        }
        return res;
    }
};
```

#### 122 - 买卖股票的最佳时机ii

![avatar](figs/25.jpeg)

__长交易分解__ 把一整段交易可以拆成每天买入每天卖出， 然后组合即为所有交易的情况；

所以最后最大的盈利 就是将每一天卖出买入为正的所有情况加起来；


```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        for(int i = 0; i + 1 < prices.size(); i++)
            res += max(0, prices[i + 1] - prices[i]);
        return res;
    }
};
```

#### 123 - 买卖股票的最佳时机iii

这题当然可以用DP去做了， **但是可以对于这种分成两段的问题，可以枚举中间的分界点，前后缀分离来完成；**

![avatar](figs/26.jpeg)

先遍历一遍记录前半段的最大利润`f[i]`, 再从后往前遍历 求得后半段的最大值即可；

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        // 最多可以完成两笔交易 -> 枚举两次交易的分界点 前后缀分解
        int n = prices.size();
        vector<int> f(n + 2);
        for(int i = 1, minp = INT_MAX; i <= n; i++)
        {
            f[i] = max(f[i - 1], prices[i - 1] - minp);
            minp = min(minp, prices[i - 1]);
        }
        int res = 0;
        for(int i = n, maxp = 0; i; i--)
        {
            res = max(res, maxp - prices[i - 1] + f[i - 1]);
            maxp = max(maxp, prices[i - 1]);
        }
        return res;
    }
};
```

#### 124 - 二叉树的最大路径和

一般树🌲中枚举路径 都是枚举最高点 （LCA 最近公共祖先）

再去找左子树最大路径和，右子树的最大路径和 （因为两边独立）

这样dfs的返回值是沿着当前节点往下走的最大路径和：三种情况 当前点停下， 向左走， 向右走

并维护一个全局的答案 记录左右两边和的最大值；

这里左右两边都是可选可不选的；

```cpp
class Solution {
public:
    int res = INT_MIN;
public:
    int dfs(TreeNode* root) {
        if(root == nullptr) return 0;
        int l = dfs(root->left);
        int r = dfs(root->right);
        //res = max(res, l + r + root->val); // 这是不对的 左右都可以不选
        res = max(res, root->val);
        res = max(res, root->val + l);
        res = max(res, root->val + r);
        res = max(res, root->val + l + r);
        return max(0, max(l, r)) + root->val;
    }
    int maxPathSum(TreeNode* root)
    {
        dfs(root);
        return res;
    }
};
```

#### 125 - 验证回文串

基础双指针遍历, 记一下几个字符的库函数

> isdigit: 判断字符是否是0-9的数字
> isalpha: 判断字符是否是字符 大小写都行
> isalnum: 上面两个的综合
> tolower/toupper: 大小写字符转换，不是字母的话会直接返回传入的字符

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int l = 0, r = s.size() - 1;
        while(l < r)
        {
            while(l < r && !isalnum(s[l])) l++;
            while(l < r && !isalnum(s[r])) r--;
            if(l < r && tolower(s[l]) != tolower(s[r])) return false;
            l++, r--;
        }
        return true;
    }
};
```

<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-06 15:33:10
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-06 19:25:10
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week18.md
-->

# Week 18 - Leetcode 171 - 180

#### 171 - Excel表列序号

感觉是168的反转版， 方法类似

```cpp
class Solution {
public:
    int titleToNumber(string s) {
        int res = 0;
        for(auto x : s)
            res = res * 26 + (x - 'A' + 1);
        return res;
    }
};
```

#### 172 - 阶乘后的零

一个数末尾的0 -> 取决于因式分解中能找到多少个10 -> 多少个 2 * 5;

所以质因数分解，找到2因子的个数`a` 5因子的个数`b` 取`min(a, b)`即可

分解质因数就是试除法 复习一下试除法求质因数

```cpp
void divide(int x)
{
    for(int i = 2; i <= x / i; i ++)
    {
        if(x % i == 0)
        {
            int s = 0;
            while(x % i == 0) x /=i, s++;
            cout << x << ' ' << s <<endl;
        }
        // ! 注意这里
        if(x > 1) cout << x << ' ' << 1 << endl;
    }
}
```

当然 对于本题不能硬分解, **对于求`n!`的质因子分解**；

1. `1 - n` 中 p 的倍数   -> n / p 下取整
2. `1 - n` 中 p^2 的倍数 -> n / p^2 下取整
...

**把他们都加起来 最终得到的就是 p 的所有因子个数**

举例来说
![avatar](figs/34.jpeg)
![avatar](figs/35.jpeg)

最后就是求一下5的质因子个数就好了hh


```cpp
class Solution {
public:
    int trailingZeroes(int n) {
        int res = 0;
        while(n)
        {
            res += n / 5;
            n /= 5;
        }
        return res;
    }
};
```

数论真的好难 学不会我本人了

#### 173 - 二叉搜索树迭代器

提到二叉搜索树一定就是中序遍历了，二叉搜索树的充分必要条件就是中序遍历结果是有序的；

这里显然用到的是迭代形式的中序遍历-> 复习一下迭代形式的二叉树中序遍历

```cpp
while(root || stk.size())
{
    while(root)
    {
        stk.push(root);
        root = root->left;
    }
    root = stk.top();
    stk.pop();
    // 处理root
    // * * *
    root = root->right; 
}
```

这里显然就是把while循环，变成每调用一次next 执行一次，hasNext 就是while的循环判断条件;

```cpp
class BSTIterator {
public:
    stack<TreeNode*> stk;
    TreeNode* root;
public:
    BSTIterator(TreeNode* root) {
        this->root = root;
    }
    
    int next() {
        int res;
        while(root)
        {
            stk.push(root);
            root = root->left;
        }
        root = stk.top();
        stk.pop();
        // 处理 root
        res = root->val;
        root = root->right;
        return res;
    }
    
    bool hasNext() {
        if(root || stk.size()) return true;
        else return false;
    }
};
```

#### 174 - 地下城游戏

**倒着dp**应该是 或者二分找初始值（二分：有单调性）；

二分找初始值复杂度会更高一些， 倒着dp就是从终点开始，使得终点的最终健康值为0(?) 推到起始点

> `f[i, j]` 所有从`(i, j)` 走到终点的路径中 需要的血量的最小值

此题不能直接从正向动态规划的原因是不确定起始点的值，但我们可以发现，到终点之后健康值为 1 一定是最优的。
可以考虑从终点到起点进行动态规划。
设状态 `f(i,j)` 表示从 `(i, j)` 成功到达终点，`(i, j)` 处需要具备的最少健康值。

初始时，`f(m−1,n−1)`为 `−max(dungeon[m−1][n−1],0)+1`，其余为正无穷。

转移时，`f(i,j)=min(f(i+1,j),f(i,j+1))−dungeon[i][j]`
如果 `f(i,j)<=0`，表示道路上的补给实在太多了，但**此时健康值不能小于0**，所以此时需要修正 `f(i,j)=1`，即下限为 `1`最终答案为 `f(0,0)`

```cpp
class Solution {
public:
    int calculateMinimumHP(vector<vector<int>>& dungeon) {
        int n = dungeon.size(), m = dungeon[0].size();
        vector<vector<int>> f(n, vector<int>(m, 1e8));
        for(int i = n - 1; i >= 0; i--)
            for(int j = m - 1; j >= 0; j--)
            {
                if(i == n - 1 && j == m - 1)
                    f[i][j] = 1 - dungeon[i][j];
                if(i + 1 < n)
                    f[i][j] = f[i + 1][j] - dungeon[i][j];
                if(j + 1 < m)
                    f[i][j] = min(f[i][j], f[i][j + 1] - dungeon[i][j]);
                f[i][j] = max(1, f[i][j]);
            } 
        return f[0][0];
    }
};
```

#### 179 - 最大数

要定义一种新的比较策略；

怎么比较A 和 B 的大小关系 -> AB 与 BA的关系 （比较谁放在前面合适）

主要是这里和数字并没什么关系。。 也找不到一个规律

记住STL重载比较函数的方式 -> 新建一个struct 重载括号

**什么样的比较关系能排序呢？ 全序关系**

全序关系需要满足以下条件：

- 反对称性：若`a ≤ b` 且 `b ≤ a` 则 `a = b`  -> ab = ba
- 传递性：若`a ≤ b` 且 `b ≤ c` 则 `a ≤ c` -> `ab ≤ ba` `bc ≤ cb` -> `ac ≤ ca`
  这里 `abc ≤ bac ≤ cab ≤ cba` 所以 `abc ≤ cba` 所以 `ac ≤ ca`
- 完全性：`a ≤ b` 或者 `b ≤ a` 这个显然


```cpp
class Solution {
public:
    struct Cmp
    {
        bool operator()(int a, int b) const
        {
            string sa = to_string(a), sb = to_string(b);
            string sab = sa + sb, sba = sb + sa;
            return sab > sba;
        }
    };
    string largestNumber(vector<int>& nums) {
        // 正好复习一下STL里面内置容器重新定义排序规则
        sort(nums.begin(), nums.end(), Cmp());
        stringstream ss;
        for(int x : nums)
            ss << x;
        string res = ss.str();
        if(res[0] != '0')
            return res;
        else
            return "0";
    }
};
```

除了自定义比较结构体之外，C++ 11 可以传入lambda 函数


> [capture list] (params list) mutable exception-> return type { function body }

```cpp
class Solution {
public:
    string largestNumber(vector<int>& nums) {
        // 正好复习一下STL里面内置容器重新定义排序规则
        sort(nums.begin(), nums.end(), [](int a, int b) -> bool 
        {
            string sa = to_string(a), sb = to_string(b);
            string sab = sa + sb, sba = sb + sa;
            return sab > sba;
        });
        stringstream ss;
        for(int x : nums)
            ss << x;
        string res = ss.str();
        if(res[0] != '0')
            return res;
        else
            return "0";
    }
};
```
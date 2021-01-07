<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-07 15:59:34
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-07 17:09:26
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week20.md
-->

# Week 20 - Leetcode 191 - 200

#### 191 - 位1的个数

这题就是经典的lowbit运算了，也叫**汉明距离**

`x & (-x)` 得到最低位的1 和 后面的所有0构成的数值
`x & (x - 1)` 删除最低位的1剩下的数值

也就是 `x = x & (-x) + x & (x - 1)`
算是常见的位运算小技巧了

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int cnt = 0;
        while(n)
        {
            n = n & (n - 1);
            cnt++;
        }
        return cnt;
    }
};
```

#### 198 - 打家劫舍

是比较基础的dp了 也是状态机模型的一个简单应用：

`f[i, 0] 表示上一家没偷过 f[i, 1] 表示上一家偷过了`

状态转移方程：
    - `f[i][0] = max(f[i - 1][0], f[i - 1][1])` : 第i家没偷 可以从上一家偷了or没偷转移过来
    - `f[i][1] = f[i - 1][0] + nums[i - 1]` : 第i家偷了 只能从上一家没偷转移过来

空间也能优化成一维的 不写了

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 0) return 0;
        vector<vector<int>> f(n + 1, vector<int>(2, -2e9));
        f[0][0] = 0;
        for(int i = 1; i <= n; i++)
        {
            f[i][0] = max(f[i - 1][0], f[i - 1][1]);
            f[i][1] = f[i - 1][0] + nums[i - 1];
        }
        return max(f[n][0], f[n][1]);
    }
};
```

#### 199 - 二叉树的右视图

这是什么 层序遍历的最后一个元素；

层序遍历就BFS好了;

```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        queue<TreeNode*> q;
        vector<int> res;
        if(!root) return res;
        q.push(root);
        while(q.size())
        {
            int cnt = q.size();
            TreeNode* cur = nullptr;
            while(cnt--)
            {
                cur = q.front();
                q.pop();
                if(cur->left)  q.push(cur->left);
                if(cur->right) q.push(cur->right);
            }
            res.push_back(cur->val);
        }
        return res;
    }
};
```

#### 200 - 岛屿数量

宇宙无敌经典搜索题，判重记得在原数组上改就好了；

```cpp
class Solution {
public:
    int n, m;
    int dx[4] = {1, 0, -1, 0}, dy[4] = {0, 1, 0, -1};
    int cnt = 0;
public:
    int numIslands(vector<vector<char>>& grid) {
        if(grid.size() == 0 || grid[0].size() == 0) return 0;
        n = grid.size(), m = grid[0].size();
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
                if(grid[i][j] == '1')
                {
                    dfs(grid, i, j);
                    cnt++;
                }
        return cnt;
    }
    void dfs(vector<vector<char>>& grid, int sx, int sy)
    {
        for(int i = 0; i < 4; i++)
        {
            int x = sx + dx[i], y = sy + dy[i];
            if(x < 0 || x >= n || y < 0 || y >= m) continue;
            if(grid[x][y] != '1') continue;
            grid[x][y] = '2'; // 无需额外开判重数组 直接在grid上面改就好
            dfs(grid, x, y);
        }
    }
};
```

<span style="color:red;"> 200题Clear! </span>
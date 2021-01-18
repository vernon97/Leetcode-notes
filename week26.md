<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-18 19:31:42
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-18 19:45:13
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week26.md
-->
# Week 26 - Leetcode 251 - 260

#### 257 - 二叉树的所有路径

基础dfs题

```cpp
class Solution {
public:
    vector<string> res;
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        if(root == nullptr) return res;
        vector<int> path;
        dfs(root, path);
        return res;
    }
    void dfs(TreeNode* root, vector<int>& path)
    {
        if(root->left == nullptr && root->right == nullptr)
        {
            stringstream ss;
            for(int x : path)
                ss << to_string(x) << "->";
            ss << to_string(root->val);
            res.push_back(ss.str());
            return;
        }
        // * left 和 right
        path.push_back(root->val);
        if(root->left)  dfs(root->left,  path);
        if(root->right) dfs(root->right, path);
        path.pop_back();
    }
};
```

#### 258 - 各位相加

> 你可以不使用循环或者递归，且在 O(1) 时间复杂度内解决这个问题吗？

o(1)是不是有公式呀..

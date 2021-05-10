<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-05-07 11:42:23
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-05-10 11:04:53
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week34.md
-->
# Week 34 - Leetcode 331 - 340

### 331 - 验证二叉树的前序序列化

**递归遍历** 是我学不会了 

左右子树必须有东西（`#`也算）所以以这个作为遍历的基础 递归判断 只需从左到右遍历数组一次即可

注意的点：

- 以`,` 作为元素分割的依据，所以要在最后补上一个逗号
- 递归中发现元素遍历完——则存在一遍子树没有元素（结束`#`也没有）所以不合法
- 最后 判断所有元素遍历完才合法 比如`9, #, #, 1`

```cpp
class Solution {
public:
    bool ans = true;
public:
    bool isValidSerialization(string s) {
        int u = 0;
        s +=','; // 以逗号作为element分割依据
        dfs(s, u);
        return ans && u == s.size(); // 注意这里还要保证遍历完
    }
    void dfs(string& s, int& u)
    {
        if(u == s.size())
        {
            ans = false;
            return;
        }
        if(s[u] == '#')
        {
            u += 2;
            return;
        }
        while(s[u] != ',') u++;
        u++; // 过滤等号
        // 根 左 右
        dfs(s, u);
        dfs(s, u);
        return;
    }
};
```


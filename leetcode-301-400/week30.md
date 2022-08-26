<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-02-24 20:06:15
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-05-10 15:26:44
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week30.md
-->
# Week 30 - Leetcode 291 - 300

### 292 - Nim游戏

Nim游戏的一些概念在数论基础里面已经整理过了 这个就是最简单的Nim游戏类型

这里用到的概念就是`必胜态` `必败态`两个  0 必败态 1 2 3 是必胜态 

4 无论怎样转移都是必输 5 6 7 都是可以转移到4上导致对手必输 则自己必胜

**所以0 4 8 ... 都是必输态 所以看石子的个数能不能被4整除**

补一个Nim游戏的例子：

> 给定n堆石子，两位玩家轮流操作，每次操作可以从任意一堆石子中拿走任意数量的石子（可以拿完 但不能不拿），最后无法进行操作的人视为失败

这里的最优操作是 先手拿到一致，后手怎么操作我就在另外一堆同样操作

所以 石子 `a1, a2, a3 ... an` 如果 `a1 ^ a2 .... ^ an = 0` 则先手必败 反之先手必胜

```cpp
class Solution {
public:
    bool canWinNim(int n) {
        return n % 4;
    }
};
```

### 295 - 数据流的中位数

很经典的一个数据结构 借此机会也是背一下哈hh

如果动态维护一个有序序列-> **平衡树**

这里用到的数据结构 **对顶堆**: 用大根堆down维护左半边区间 用小根堆up维护右半边区间

> 1. 建立一个大根堆，一个小根堆。大根堆存储小于当前中位数，小根堆存储大于等于当前中位数。且小根堆的大小永远都比大根堆大1或相等。
> 2. 根据上述定义，我们每次可以通过小根堆的堆顶或者两个堆的堆顶元素的平均数求出中位数。
> 3. 维护时，如果新加入的元素大于等于当前的中位数，则加入小根堆；否则加入大根堆。然后如果发现两个堆的大小关系不满足上述要求，则可以通过弹出一个堆的元素放到另一个堆中。

```cpp
class MedianFinder {
public:
    priority_queue<int, vector<int>, greater<int>> up;
    priority_queue<int> down;
    // 保证down的元素 和up相等或者只多1
public:
    /** initialize your data structure here. */
    MedianFinder() = default;
    
    void addNum(int num) {
        if(down.empty() || num <= down.top())
        {
            down.push(num);
            if(down.size() > up.size() + 1)
            {
                up.push(down.top());
                down.pop();
            }
        }
        else
        {
            up.push(num);
            if(up.size() > down.size())
            {
                down.push(up.top());
                up.pop();
            }
        }   
    }
    double findMedian() {
        if((down.size() + up.size()) % 2) return down.top();
        else return (down.top() + up.top()) / 2.0;
    }
};
```

### 297 - 二叉树的序列化与反序列化

当然序列化有很多种啦，这里用一个深搜的方法来序列化/反序列化

如果只是前序遍历是没有办法确定二叉树的 但是加上了空节点的标记就可以了

```cpp
class Codec {
public:
    string path;
public:
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        dfs_s(root);
        return path;
    }

    void dfs_s(TreeNode* root)
    {
        if(root == nullptr) path += "#,";
        else
        {
            path += to_string(root->val) + ',';
            dfs_s(root->left);
            dfs_s(root->right);
        }
        
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        int u = 0;
        return dfs_d(data, u);
    }
    TreeNode* dfs_d(string& data, int& u) // ! 注意这里哦
    {
        if(data[u] == '#')
        {
            u += 2;
            return nullptr;
        }
        else
        {
            int k = u;
            while(data[u] != ',') u++;
            TreeNode* root = new TreeNode(stoi(data.substr(k, u - k)));
            u++; // 跳过逗号
            root->left  = dfs_d(data, u);
            root->right = dfs_d(data, u);
            return root;
        }
    
    }
};
```

### 299 - 猜数字游戏

确实没啥好说的 哈希记录重复数字

```cpp
class Solution {
public:
    string getHint(string secret, string guess) {
        int a = 0, b = 0;
        vector<int> shash(11, 0), ghash(11, 0);
        for(int i = 0; i < secret.size(); i++)
        {
            shash[secret[i] - '0']++;
            ghash[guess[i] - '0']++;
            if(secret[i] == guess[i]) a++;
        }
        for(int i = 0; i < 10; i++)
            b += min(shash[i], ghash[i]);
        return to_string(a) + "A" + to_string(b - a) + "B";
    }
};
```

### 300 - 最长递增子序列

这题LIS问题 基础动态规划了 `f[i]表示以nums[i]结尾的最长上升子序列长度`

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> f(n + 1, 1);
        int res = 1;
        for(int i = 0; i < n; i++)
            for(int j = 0; j < i; j++)
            {
                if(nums[i] > nums[j])
                {
                    f[i] = max(f[i], f[j] + 1);
                    res = max(res, f[i]);
                }
            }
        return res;
    }
};
```

复习一下记录方案的LIS 和 贪心版本nlogn的LIS


**记录方案的LIS：**

```cpp
for(int i = 1; i <= n; i++)
{
    f[i] = 0;
    g[i] = 0;
    for(int j = 1; j < i; j ++)
    {
        if(a[j] < a[i])
        {
            if(f[i] < f[j] + 1)
            {
                f[i] = f[j] + 1;
                g[i] = j;
            }
        }
    }
}
int k = 1;
for(int i = 1; i <= n; i++)
    if(f[k] < f[i])
        k = i;
for(int i = 0, len = f[k]; i < len; i++)
{
    printf("%d ", a[k]);
    k = g[k];
}
```

**o(nlogn)的**

```cpp
    f[cnt++] = w[0];
    for (int i = 1; i < n; i++) {
        if (w[i] > f[cnt-1]) f[cnt++] = w[i];
        else {
            int l = 0, r = cnt - 1;
            while (l < r) {
                int mid = (l + r) >> 1;
                if (f[mid] >= w[i]) r = mid;
                else l = mid + 1;
            }
            f[r] = w[i];
        }
    }
    cout << cnt << endl;
```
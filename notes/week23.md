<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-11 16:23:08
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-12 22:11:59
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week23.md
-->
<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-11 16:23:08
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-11 22:25:24
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week23.md
-->
- [Week 23 - Leetcode 221 - 230](#week-23---leetcode-221---230)
      - [221 - 最大正方形](#221---最大正方形)
      - [222 - 完全二叉树的节点个数](#222---完全二叉树的节点个数)
      - [223 - 矩形面积](#223---矩形面积)
      - [224 - 基本计算器](#224---基本计算器)
      - [225 - 用队列实现栈](#225---用队列实现栈)
      - [226 - 翻转二叉树](#226---翻转二叉树)
      - [227 - 基本计算器II](#227---基本计算器ii)
      - [228 - 汇总区间](#228---汇总区间)
      - [229 - 求众数II](#229---求众数ii)
      - [230 - 二叉搜索树中第K小的元素](#230---二叉搜索树中第k小的元素)

# Week 23 - Leetcode 221 - 230

#### 221 - 最大正方形

这题 是经典的动态规划了，要复习一下
（记住只有最大正方形是动态规划 最大矩形不是）

说着就来回顾一下最大矩形(Leetcode 85. 最大矩形) 的那个题是怎么做的； 按层遍历 + 单调栈

这题就是动态规划了, 动态规划就是见一个记一个hhh

> `f[i, j]` 表示以`(i, j)` 为右下角的最大正方形边长

`f[i, j] = min(f[i - 1, j], f[i, j - 1], f[i - 1, j - 1]) + 1`

![avatar](../figs/44.jpeg)

代码没什么好说的 主要是这个状态转移方程很难想

```cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        if(matrix.empty() || matrix[0].empty()) return 0;
        int n = matrix.size(), m = matrix[0].size();
        vector<vector<int>> f(n + 1, vector<int>(m + 1, 0));

        int res = 0;
        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= m; j++)
                if(matrix[i - 1][j - 1] == '1')
                {
                    f[i][j] = min(f[i - 1][j], min(f[i][j - 1], f[i - 1][j - 1])) + 1; 
                    res = max(res, f[i][j]);
                }
        return res * res;
    }
};
```

#### 222 - 完全二叉树的节点个数

只记得完全二叉树可以用数组模拟了.. 

完全二叉树 和 满二叉树的区别 别记成一样的就行了

**这题居然是二分？！**

![avatar](../figs/45.jpeg)

```cpp
class Solution {
public:
    int countNodes(TreeNode* root) {
        if(!root) return 0;
        TreeNode* l = root->left, *r = root->right;
        int x = 1, y = 1;
        while(l) l = l->left,  x++;
        while(r) r = r->right, y++;
        if(x == y) return (1 << x) - 1;
        return 1 + countNodes(root->left) + countNodes(root->right);
    }

};
```

#### 223 - 矩形面积

平面两个线段`[A, B]` 和 `[C, D]`的重合部分：`min(B, D) - max(A, C)`

请注意这里即不用排序，也不必预先判断不重合的情况（因为不重合相减完是小于0的）

就计算两条边的重叠部分 用两个矩形面积减去重叠部分面积即可；

爆int很烦 -> 改成long long

```cpp
class Solution {
public:
    int computeArea(long long A, long long B, long long C, long long D, long long E, long long F, long long G, long long H) {
        // 矩形多了 肯定就是扫描线 + 线段树了 但是就两个。。
        // 怎么判断矩形是否重叠？
        long long area1 = (C - A) * (D - B), area2 = (G - E) * (H - F);
        // 判断是否重叠 -> 转化为判断线段交集
        // ab cd -> ac eg
        long long dupX = min(C, G) - max(A, E);
        // ab cd -> bd fh
        long long dupY = min(D, H) - max(B, F);
        if(dupX > 0 && dupY > 0)
            return area1 + area2 - dupX * dupY;
        else return area1 + area2;
    }
};
```

#### 224 - 基本计算器

```diff
+ 表达式求值
```

这个题纯纯模板题了, 方案是**将中缀表达式转化为后缀表达式后求值**

↓下面的是终极版计算器 中缀表达式转化为后缀表达式 运算符分优先级 计算

```cpp
/*
中缀表达式求值，有两种做法：
1. 转换为后缀表达式再求值：O(N)
2. 递归法求值：O(N^2)
这里使用方法 1
注意这个题目还要处理负数的符号位的情况，例如 -3+1
- 到底是负号还是减号?
*/
#include <cmath>
#include <iostream>
#include <vector>
using namespace std;

vector<int> nums;
vector<char> ops;
string s;

// 优先级
int grade(char op) {
    switch (op) {
        case '(':
            return 1;
        case '+':
        case '-':
            return 2; 
        case '*':
        case '/':
            return 3;
        case '^':
            return 4;
    }
    return 0;
}
// 处理后缀表达式中的一个运算符
void calc(char op) {
    // 从栈顶取出两个数
    int y = nums.back();
    nums.pop_back();
    int x = 0;
    // 如果出现负号的情况：
    // 可能没有多的操作数了，前一个操作数当做 0 看待
    if (nums.size()) {
        x = nums.back();
        nums.pop_back();
    }
    int z;
    switch (op) {
        case '+':
            z = x + y;
            break;
        case '-':
            z = x - y;
            break;
        case '*':
            z = x * y;
            break;
        case '/':
            z = x / y;
            break;
        case '^':
            z = pow(x, y);
    }
    // 把运算结果放回栈中
    nums.push_back(z);
}
int main() {
    cin >> s;
    int val = 0;
    for (int i = 0; i < s.length(); i++) {
        // 多位数， 并且表达式是使用字符串逐字符存储的，我们只需要稍加判断，把连续的一段数字看成一个数即可。
        if (s[i] >= '0' && s[i] <= '9') {
            val = val * 10 + (s[i] - '0'); // 这里 - ‘0’ 这里要加括号！ 避免溢出 
            if (s[i + 1] >= '0' && s[i + 1] <= '9') continue;
            // 后缀表达式的一个数，直接入栈
            nums.push_back(val);
            val = 0;
        }
        // 左括号：直接入栈
        else if (s[i] == '(')
            ops.push_back('(');
        // 右括号：一直出栈直到遇见左括号
        else if (s[i] == ')') {
            while (ops.size() && ops.back() != '(') {
                // 出栈一个符号就计算一下
                char op = ops.back();
                ops.pop_back();
                calc(op);
            }
            // 左括号出栈（如果有的话）
            if (ops.size()) ops.pop_back();
        }
        // 运算符：只要栈顶符号的优先级不低于新符号，就不断取出栈顶并输出，最后把新符号进栈，其实只在这里使用了优先级的比较
        else {
            while (ops.size() && grade(ops.back()) >= grade(s[i])) {
                char op = ops.back();
                ops.pop_back();
                calc(op);
            }
            ops.push_back(s[i]);
        }
    }
    // 剩下符号再计算一下
    while (ops.size()) {
        char op = ops.back();
        ops.pop_back();
        calc(op);
    }
    // 后缀表达式栈中最后剩下的数就是答案
    cout << nums.front() << endl;
    return 0;
}
```

所以对于本题

```cpp
class Solution {
public:
    stack<int> nums;
    stack<char> ops;
public:
    void calc(char op)
    {
        // 记住是先取y 再取x
        int y = nums.top(); nums.pop();
        int x = 0; 
        if (nums.size()) {
            x = nums.top();
            nums.pop();
        }
        int z = 0;
        switch(op)
        {
            case '+':
                z = x + y;
                break;
            case '-':
                z = x - y;
                break;
        }
        nums.push(z);
    }
    int calculate(string s) {
        for(int i = 0; i < s.size(); i++)
        {
            // * case 1 : 空格
            if(s[i] == ' ') continue;
            // * case 2 : 多位数字
            if(isdigit(s[i]))
            {
                int x = 0, j = i;
                while(j < s.size() && isdigit(s[j])) x = x * 10 + (s[j++] - '0');
                i = j - 1;
                nums.push(x);
            }
            // * case 3 : 左括号
            else if (s[i] == '(')  ops.push('(');
            // * case 4 : 右括号
            else if (s[i] == ')')
            {
                while(ops.size() && ops.top() != '(')
                {
                    char op = ops.top();
                    ops.pop();
                    calc(op);
                }
                ops.pop();
            }
            // * case 5 : 运算符
            else
            {
                // 这里由于没有高级运算符 只有 + - 那么直接算就好了
                while(ops.size() && ops.top() != '(')
                {
                    char op = ops.top();
                    ops.pop();
                    calc(op);
                }
                ops.push(s[i]);
            }
        }
        // 最后的处理
        while(ops.size())
        {
            char op = ops.top();
            ops.pop();
            calc(op);
        }
        return nums.top();
    }
};
```

#### 225 - 用队列实现栈

好没劲这题, 用队列实现栈的意思就是每次取一个元素就要倒腾一圈

```cpp
class MyStack {
public:
    queue<int> q;
public:
    /** Initialize your data structure here. */
    MyStack() = default;
    
    /** Push element x onto stack. */
    void push(int x) {
        q.push(x);
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int k = q.size();
        while(--k)
        {
            int t = q.front();
            q.pop();
            q.push(t);
        }
        int res = q.front();
        q.pop();
        return res;
    }
    /** Get the top element. */
    int top() {
        int k = q.size();
        while(--k)
        {
            int t = q.front();
            q.pop();
            q.push(t);
        }
        int res = q.front();
        q.pop();
        q.push(res);
        return res;

    }
    /** Returns whether the stack is empty. */
    bool empty() {
        return q.empty();
    }
};
```

#### 226 - 翻转二叉树

先翻转左右儿子 再递归操作就好了

(为什么题目描述还要鞭尸Homebrew的作者)

```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(!root || (!root->left && !root->right)) return root;
        // 递归构建
        TreeNode* tmp = root->right;
        root->right = root->left;
        root->left = tmp;
        invertTree(root->left);
        invertTree(root->right);
        return root;
    }
};
```

#### 227 - 基本计算器II

和上一题一样，加上了运算符优先级判断；

```cpp
class Solution {
public:
    stack<int> nums;
    stack<char> ops;
    unordered_map<char, int> priority;
public:
    void calc(char op)
    {
        // 记住是先取y 再取x
        int y = nums.top(); nums.pop();
        int x = 0; 
        if (nums.size()) {
            x = nums.top();
            nums.pop();
        }
        int z = 0;
        switch(op)
        {
            case '+':
                z = x + y;
                break;
            case '-':
                z = x - y;
                break;
            case '*':
                z = x * y;
                break;
            case '/':
                z = x / y;
                break;
        }
        nums.push(z);
    }
    int calculate(string s) {
        priority['+'] = priority['-'] = 2;
        priority['*'] = priority['/'] = 3;
        priority['('] = 1; // '(' 身份最低 到他一定要结束
        for(int i = 0; i < s.size(); i++)
        {
            // * case 1 : 空格
            if(s[i] == ' ') continue;
            // * case 2 : 多位数字
            if(isdigit(s[i]))
            {
                int x = 0, j = i;
                while(j < s.size() && isdigit(s[j])) x = x * 10 + (s[j++] - '0');
                i = j - 1;
                nums.push(x);
            }
            // * case 3 : 左括号
            else if (s[i] == '(')  ops.push('(');
            // * case 4 : 右括号
            else if (s[i] == ')')
            {
                while(ops.size() && ops.top() != '(')
                {
                    char op = ops.top();
                    ops.pop();
                    calc(op);
                }
                ops.pop();
            }
            // * case 5 : 运算符
            // ! 注意和上一题的区别就在这里
            else
            {
                // ! 如果当前栈顶运算符优先级大于等于s[i]
                while(ops.size() && priority[ops.top()] >= priority[s[i]])
                {
                    char op = ops.top();
                    ops.pop();
                    calc(op);
                }
                ops.push(s[i]);
            }
        }
        // 最后的处理
        while(ops.size())
        {
            char op = ops.top();
            ops.pop();
            calc(op);
        }
        return nums.top();
    }
};
```

#### 228 - 汇总区间

感觉就是题意的简单模拟

```cpp
class Solution {
public:
    vector<string> summaryRanges(vector<int>& nums) {
        // 模拟题
        vector<string> res;
        for(int i = 0; i < nums.size(); i++)
        {
            if(i + 1 < nums.size() && nums[i] + 1 == nums[i + 1])
            {
                int j = i;
                while(j + 1 < nums.size() && nums[j] + 1 == nums[j + 1]) j++;
                res.push_back(to_string(nums[i]) + "->" + to_string(nums[j]));
                i = j;
            }
            else
                res.push_back(to_string(nums[i]));
        }
        return res;
    }
};
```

#### 229 - 求众数II

先来复习一下 (Leetcode 169. 多数元素)

```cpp
遍历nums, x = nums[0], cnt = 1;
for(int i = 1; i < nums.size(); i++)
{
    if(nums[i] == x) cnt++;
    else cnt--;
    if(cnt == 0) x = nums[i];
}
```

> **摩尔投票算法**

![avatar](../figs/46.jpeg)

```cpp
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int r1 = 0, cnt1 = 0, r2 = 0, cnt2 = 0, n = nums.size();
        vector<int> res;
        for(int x : nums)
        {
            if(cnt1 && x == r1) cnt1++;
            else if(cnt2 && x == r2) cnt2++;
            else if(!cnt1) r1 = x, cnt1++;
            else if(!cnt2) r2 = x, cnt2++;
            else cnt1--, cnt2--;
        }
        // 得到备选
        cnt1 = cnt2 = 0;
        for(int x : nums)
        {
            if(x == r1) cnt1++;
            else if(x == r2) cnt2++;
        }
        if(cnt1 > n / 3) res.push_back(r1);
        if(cnt2 > n / 3) res.push_back(r2);
        return res;
    }
};
```

这里我们简单证明一下为什么摩尔投票法是正确的；

证明的点在于：**出现次数大于`n / k`的数`r`一定会被选出来**

> **如果想抵消一个`r`的话 至多抵消`k - 1`个其他元素;**

所以, 出现次数大于`n / k`的数`r` 肯定会被剩下, 这便是摩尔投票法正确性的证明✅

#### 230 - 二叉搜索树中第K小的元素

说到BST中第K小的元素，马上就回顾一下Leetcode.215 数组中的第K个最大元素

215是快速选择算法了

```cpp
int quick_select(int l, int r, int k)
{
    if(l >= r) return nums[r];
    int i = l - 1, j = r + 1, x = nums[l + r >> 1];
    while(l < r)
    {
        do i++; while(nums[i] < x);
        do j--; while(nums[j] > x);
        if(i < j) swap(nums[i], nums[j]);
    }
    int sl = j - l + 1;
    if(sl < k) return quick_select(l, j, k);
    else return quick_select(j + 1, r, k - sl);
}
```

而这个题, **二叉搜索树的中序遍历是有序数组** 就跑一遍中序遍历 遍历到第K个数返回就可以了;

很快啊 就复习一下 迭代法的中序遍历(左根右);

前序遍历是加入时处理，中序遍历是弹出时处理

**前序遍历**

```cpp
void firstOrderTraverse(TreeNode* root)
{
    stack<TreeNode*> stk;
    vector<int> res;
    while(root || stk.size())
    {
        while(root)
        {
            res.push_back(root->val);
            stk.push(root);
            root = root->left;
        }
        root = stk.top()->right;
        stk.pop();
    }    
}
```

**中序遍历**

```cpp
void inOrderTraverse(TreeNode* root)
{
    stack<TreeNode*> stk;
    vector<int> res;
    while(root || stk.size())
    {
        while(root)
        {
            stk.push(root);
            root = root->left;
        }
        root = stk.top();
        res.push_back(root->val);
        stk.pop();
        root = root->right;
    }
}
```

↓本题代码

```cpp
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        stack<TreeNode*> stk;
        int cnt = 0;
        while(root || stk.size())
        {
            while(root)
            {
                stk.push(root);
                root = root->left;
            }
            root = stk.top();
            cnt++;
            if(cnt == k) return root->val;
            stk.pop();
            root = root->right;
        }
        return 0;
    }
};
```

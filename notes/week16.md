<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-12-30 21:17:53
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-03 04:03:11
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week16.md
-->
# Week 16 - 151 - 160

#### 151 - 翻转字符串里的单词

分为两步，举个例子来说：
`s = "the sky is blue"`

1. 将字符串整体翻转
`s = "eulb si yks eht"`

2. 以单词为单位翻转
`s = "blue is sky the"`

就好了，这里按照空格分割单词可以用到c++的`stringstream`来处理， 初始化stringstream 之后

`while(ss >> word)` 就可以按空格将单词分开， 此外额外的空格也会被直接处理

```cpp
class Solution {
public:
    string reverseWords(string s) {
        reverse(s.begin(), s.end());
        stringstream ss(s), res;
        string word;
        while(ss >> word)
        {
            reverse(word.begin(), word.end());
            res << word << ' ';
        }
        string ans = res.str();
        ans.pop_back();
        return ans;
    }
};
```

#### 152 - 乘积最大子数组

回忆一下和最大的子数组的求法， 就是简单的动态规划
`f[i] = max(f[i - 1], 0) + nums[i]` 

表示有两种转移方式，从上一个子数组延续过来，或者另起一段以当前为开头 共两种选择；

这个乘积最大的子数组 和上一题不同的地方在于 有负负得正的情况存在
，所以除了最大乘积`f`之外 要额外记录一个 最小乘积`g`;

最后有三种情况：

1. 自己独立开启一段`nums[i]`
2. 最大乘积 接着乘 `f[i - 1] * nums[i]`
3. 负负得正 接着乘 `g[i - 1] * nums[i]`

然后空间可以优化成一维的；

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        // 一看就是动态规划
        int f = nums[0], g = nums[0], res = nums[0];
        for(int i = 1; i < nums.size(); i++)
        {
            int a = nums[i], fa = f * a , ga = g * a;
            f = max(a, max(fa, ga));
            g = min(a, min(fa, ga));
            res = max(res, f);
        }
        return res;
    }
};
```

#### 153 - 寻找旋转排序数组中的最小值

实际上这两个题和前面的 **33, 34** 是一样的，甚至还简化了

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        // 和之前的题很像诶 二分找分界线 
        int k = nums[0];
        int l = 0, r = nums.size() - 1;
        while(l < r)
        {
            int mid = l + r >> 1;
            if(nums[mid] < k) r = mid;
            else l = mid + 1;
        }
        return min(nums[0], nums[l]);
    }
};
```

#### 154 - 寻找旋转排序数组中的最小值II

同样的， 也是把后面和`nums[0]`相等的部分提前删去就好了

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while(l < r && nums[l] == nums[r]) r--;
        // 二分
        while(l < r)
        {
            int mid = l + r >> 1;
            if(nums[mid] < nums[0]) r = mid;
            else l = mid + 1;
        }
        return min(nums[0], nums[l]);
    }
};
```

#### 155 - 最小栈

在维护正常的栈的基础上，额外维护一个前缀最小值栈`f`, 来记录stack 前`i`个元素中的最小值就好了


```cpp
class MinStack {
public:
    stack<int> stk, f;
public:
    /** initialize your data structure here. */
    MinStack() {
    }
    
    void push(int x) {
        stk.push(x);
        if(f.empty() || f.top() > x) f.push(x);
        else f.push(f.top());
    }
    
    void pop() {
        stk.pop();
        f.pop();
    }
    
    int top() {
        return stk.top();
    }
    
    int getMin() {
        return f.top();
    }
};
```

**[付费题就先跳过]**

#### 160 - 相交链表

这题的思路还挺巧妙的, 记一下

![avatar](../figs/32.jpeg)

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(!headA || !headB) return 0;
        ListNode* a = headA, *b = headB;
        while(a != b)
        {
            if(a == nullptr)
                a = headB;
            else
                a = a->next;
            if(b == nullptr)
                b = headA;
            else
                b = b->next;
        }
        if(a == nullptr) return 0;
        else return a;
    }
};
```

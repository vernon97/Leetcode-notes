<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-12-04 22:13:27
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-12-08 20:27:31
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week09.md
-->
# Week 09 - Leetcode 81 - 90

#### 81 - 搜索旋转排序数组ii

这题与上一题的区别就在于可能有重复元素的出现，这样就不能保证存在一个分界线使得分成两段，一段在上一段在下；

如果前后存在相同的一段元素 比如 `[1,1,1,2,2,2,1,1,1]` 这样我们第一次二分找旋转位置的方法就失效了；
所以要预处理，删除最后与最前的重复部分， 变成`[1,1,1,2,2,2]` 在按照上一题的做法两次二分就好了；

所以最坏程度下复杂度是`o(n)`

```cpp
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        if(nums.empty()) return false;
        if(target == nums[0]) return true;
        // 预处理 删除最后一段的重复元素
        int l = 0, r = nums.size() - 1;
        while(r > 0 && nums[0] == nums[r]) r--;
        int r_cpy = r;
        // 1. 二分寻找旋转位置
        while(l < r)
        {
            int mid = l + r + 1 >> 1;
            if(nums[0] <= nums[mid]) l = mid;
            else r = mid - 1;
        }
        // 2. 找到在那一段 二分
        if(target >= nums[0]) l = 0;
        else l++, r = r_cpy;
        while(l < r)
        {
            int mid = l + r >> 1;
            if(target <= nums[mid]) r = mid;
            else l = mid + 1;
        }
        return nums[r] == target;
    }
};
```

#### 82 - 删除排序链表中的重复元素ii

预先记录一个prev节点，通过记录重复出现次数`cnt`

如果`cnt>1`，就证明是重复元素 prev指向直接跳过所有重复元素
反之就直接继续；

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(head == nullptr) return head;
        // 删除所有包含重复元素的节点
        ListNode* dummy = new ListNode(0), *cur = head, *prev = dummy;
        dummy->next = head;
        while(cur)
        {
            int val = cur->val, cnt = 0;
            ListNode* t = cur;
            while(t && t->val == val) t = t->next, cnt++;
            if(cnt > 1) prev->next = t;
            else prev = prev->next;
            cur = t;
        }
        return dummy->next;
    }
};
```

#### 83 - 删除排序链表中的重复元素

相较于上一题还比较简单，直接把重复元素过掉就好了

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* cur = head;
        // dummy->next = head;
        while(cur)
        {
            int val = cur->val;
            ListNode* t = cur;
            while(t && t->val == val) t = t->next;
            cur = cur->next = t;
        }
        return head;
    }
};
```

#### 84 - 柱状图中最大的矩形

```diff
+ 单调栈
```

这一题可以说是单调栈的典型应用了;

![avatar](/../figs/16.jpeg)

>单调栈： 寻找左边/右边 第一个比当前元素小/大的位置

每个元素只会出栈/进栈一次 所以最终的时间复杂度是o(n)的;

**stack是没有clear方法的，要清空栈的话要`stack<int>().swap(stk)`**

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        // 单调栈 左边第一个比他小的元素 右边第一个比他小的元素
        int res = 0, n = heights.size();
        if(!n) return res;
        vector<int> left(n), right(n);
        stack<int> stk;
        for(int i = 0; i < n; i++)
        {
            while(stk.size() && heights[stk.top()] >= heights[i]) stk.pop();
            if(stk.empty()) left[i] = -1;
            else left[i] = stk.top();
            stk.push(i);
        }
        stack<int>().swap(stk);
        for(int i = n - 1; i >= 0; i--)
        {
            while(stk.size() && heights[stk.top()] >= heights[i]) stk.pop();
            if(stk.empty()) right[i] = n;
            else right[i] = stk.top();
            stk.push(i);
        }
        for(int i = 0; i < n; i++)
            res = max(res, (right[i] - left[i] - 1) * heights[i]);      
        return res; 
    }
};
```

#### 85 - 最大矩形

这题思路还挺难的，实际是上一个题的扩展；

![avatar](../figs/17.jpeg)
heights可以逐层递推得到

```cpp
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int res = 0;
        if(matrix.empty() || matrix[0].empty()) return res;
        int n = matrix.size(), m = matrix[0].size();
        vector<int> heights(m, 0);
        for(int i = 0; i < n; i++)
        {
            for(int j = 0; j < m; j++)
                if(matrix[i][j] == '1') heights[j]++;
                else heights[j] = 0;
            res = max(res, largestRectangleArea(heights));
        }
        return res;
    }
    int largestRectangleArea(vector<int>& heights) {
        // 单调栈 左边第一个比他小的元素 右边第一个比他小的元素
        int res = 0, n = heights.size();
        if(!n) return res;
        vector<int> left(n), right(n);
        stack<int> stk;
        for(int i = 0; i < n; i++)
        {
            while(stk.size() && heights[stk.top()] >= heights[i]) stk.pop();
            if(stk.empty()) left[i] = -1;
            else left[i] = stk.top();
            stk.push(i);
        }
        stack<int>().swap(stk);
        for(int i = n - 1; i >= 0; i--)
        {
            while(stk.size() && heights[stk.top()] >= heights[i]) stk.pop();
            if(stk.empty()) right[i] = n;
            else right[i] = stk.top();
            stk.push(i);
        }
        for(int i = 0; i < n; i++)
            res = max(res, (right[i] - left[i] - 1) * heights[i]);      
        return res; 
    }
};
```

#### 86 - 分隔链表

实际上是快排的一部分; 方法是开两个链表，最后拼起来；

```cpp
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        ListNode* left = new ListNode(0), *right = new ListNode(0);
        ListNode* lt = left, *rt = right, *cur = head;
        while(cur)
        {
            if(cur->val < x)
                lt = lt->next = cur;
            else
                rt = rt->next = cur;
            cur = cur->next; 
        }
        lt->next = right->next;
        rt->next = nullptr;
        return left->next;
    }
};
```

#### 87 - 扰乱字符串

```diff
+ 搜索
```

递归判断两个字符串是否可以相互转化。

**首先判断两个字符串的字符集合是否相同**，如果不同，则两个字符串一定不可以相互转化。

**然后枚举第一个字符串左半部分的长度**，分别递归判断两种可能的情况：

**该节点不发生翻转**，则分别判断两个字符串的左右两部分是否分别可以相互转化；

**该节点发生翻转**，则分别判断第一个字符串的左边是否可以和第二个字符串的右边相互转化，且第一个字符串的右边可以和第二个字符串的左边相互转化；

> 复杂度分析：
> an = 4(a1 + a2 + ... + an-1) 相当于每一个长度遍历四次 -> 裂项相减 复杂度是`o(5^n)`

```cpp
class Solution {
public:
    bool isScramble(string s1, string s2) {

        if(s1.size() != s2.size()) return false;

        if(s1 == s2) return true;
        string bs1 = s1, bs2 = s2;
        sort(bs1.begin(), bs1.end());
        sort(bs2.begin(), bs2.end());
        if(bs1 != bs2) return false;
        int n = s1.size();
        // 这里枚举的是长度
        for(int i = 1; i <= n - 1; i++)
        {
            if(isScramble(s1.substr(0, i), s2.substr(0, i)) && isScramble(s1.substr(i), s2.substr(i)))
                return true;
            if(isScramble(s1.substr(0, i), s2.substr(n - i)) && isScramble(s1.substr(i), s2.substr(0, n - i)))
                return true;
        } 
        return false;
    }
};
```

#### 88 - 合并两个有序链表

二路归并，重点在于这个空间是如何省下的，不用另开数组而直接用`nums1`存储；

从后往前写入`nums1` 就不会覆盖到还没统计的元素；

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        // 纯纯的二路归并
        // 从后往前遍历 避免覆盖
        int k = n + m - 1;
        int i = m - 1, j = n - 1;
        while(i >= 0 && j >= 0)
        {
            if(nums1[i] < nums2[j])
                nums1[k--] = nums2[j--];
            else
                nums1[k--] = nums1[i--];
        }
        while(j >= 0)
            nums1[k--] = nums2[j--];
    }
};
```

#### 89 - 格雷编码

格雷编码的定义： `2^n`个二进制数围成一圈，使得相邻的两位只有一位不同；

举例来说：`000 - 001 - 011 - 010 - 110 - 111 - 101 - 100`

格雷码生成是有规律的 可以记一下：

![avatar](../figs/18.jpeg)

镜像复制，前半段最后补0 后半段最后补1

```cpp
class Solution {
public:
    vector<int> grayCode(int n) {
        vector<int> res(1 << n);
        if(n == 0) return res;
        res[1] = 1;
        for(int i = 2; i <= n; i++)
        {
            int l = 1 << (i - 1);
            // 1. 对称复制
            for(int j = 0; j < l; j++)
                res[2 * l - j - 1] = res[j];
            // 2. 补位
            for(int j = 0; j < l << 1; j++)
                res[j] = 2 * res[j] + j / l; 
        }
        return res;
    }
};
```

#### 90 - 子集II

枚举每个位置的数出现 or 不出现；

这里重复去除和之前的方法都是一致的，同样的元素规定一个统一的出现顺序；

这里对于重复元素的限制 必须按照顺序用 比如`[1,2,2]` 没用第一个`2`时候不允许用第二个`2`

```cpp
class Solution {
public:
    int n;
    vector<vector<int>> res;
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        // 搜索每个元素要 or 不要；
        // 这里对于重复元素的限制 必须按照顺序用 比如[1,2,2] 没用第一个2时候不允许用第二个2
        n = nums.size();
        vector<int> path;
        sort(nums.begin(), nums.end());
        dfs(0, -1, path, nums);
        return res;
    }
    void dfs(int u, int last, vector<int>& path, vector<int>& nums)
    {
        if(u == n) res.push_back(path);
        else
        {
            dfs(u + 1, last, path, nums);
            // 和上一个数一样 且上一个数没选-> 不行🚫
            if(u && nums[u - 1] == nums[u] && last != u - 1) return;
            path.push_back(nums[u]);
            dfs(u + 1, u, path, nums);
            path.pop_back(); 
        }
    }
};
```

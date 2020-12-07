<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-12-04 22:13:27
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-12-07 22:02:32
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

![avatar](/figs/16.jpeg)

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


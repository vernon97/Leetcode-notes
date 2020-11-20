<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-11-20 21:48:53
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-11-20 22:09:17
 * @FilePath: /Leetcode-notes/week03.md
-->
# Week 03 - Leetcode 21 - 30

#### 21 - 合并两个有序链表
二路归并

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        // 双指针 
        ListNode* dummy = new ListNode(0), *cur = dummy;

        while(l1 && l2)
        {
            if(l1->val < l2->val)
                cur->next = l1, l1 = l1->next;
            else
                cur->next = l2, l2 = l2->next;
            cur = cur->next;
        }

        ListNode* l = l1 ? l1 : l2;
        while(l)
        {
            cur = cur->next = l;
            l = l->next;
        }
        return dummy->next;
    }
};
```
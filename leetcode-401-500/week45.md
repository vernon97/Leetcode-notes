# Week 44 - Leetcode 441 - 450

### 441 - 排列硬币

实际上是解方程，直接二元一次方程组 或者二分下都可以

```cpp
class Solution {
public:
    int arrangeCoins(int n) {
        // 等差数列求和哈，1 + 2 + 3 .. + i = i * (i + 1) / 2 <= n
        // i * i + i <= 2n 
        // 另外也可以二分，这是满足单调性的
        unsigned long long l = 1, r = (1 << 16);
        while(l < r){
            long long mid = l + r + 1 >> 1;
            if(mid * (mid + 1) / 2 <= n) l = mid;
            else r = mid - 1;
        }
        return l;

    }
};
```

### 442 - 数组中重复的数据
本题的限制比较严格，需要不试用额外空间来统计；

1. 整数数组，元素范围在0-n -> 原地哈希，以value做下表

2. 在原数组上标记出现一次/两次：**用相反数**

另外可以优化的点在于本题只用遍历一次；再乘-1之后，如果是正的，这个数已经出现两次了；


```cpp
class Solution {
public:
    vector<int> findDuplicates(vector<int>& nums) {
        vector<int> res;
        for(int i = 0; i < nums.size(); i++){
            int val = abs(nums[i]) - 1;
            nums[val] = -nums[val];
            if(nums[val] > 0) res.push_back(val + 1);
        }

        return res;
    }
};
```

### 443 - 压缩字符串

类似于一个模拟题，直接写入原数组就可以了

```cpp
class Solution {
public:
    int compress(vector<char>& chars) {
        int k = 0;
        for(int i = 0; i < chars.size(); i++) {
            int j = i;
            while(j < chars.size() && chars[j] == chars[i]) j++;
            chars[k++] = chars[i];
            int len = j - i, t = k;
            if(len > 1){
                while(len) {
                    chars[t++] = len % 10 + '0';
                    len /= 10;
                }
                reverse(chars.begin() + k, chars.begin() + t);
                k = t;
            }
            i = j - 1;
        }
        return k;

    }
};
```

### 445 - 两数相加 ii

高精度加法的链表版本，注意下无论是数组还是链表都是倒序，从个位数相加

这里链表给定的顺序是正序，所以需要额外一步反转链表

反转链表注意head->next = nullptr这一步

```cpp
class Solution {
public:
    ListNode* reverse(ListNode* head) {
        if(!head || !head->next) return head;
        ListNode* a = head, *b = head->next;
        while(b){
            ListNode* c = b->next;
            b->next = a;
            a = b, b = c;
        }
        head->next = nullptr;
        return a;
    }
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        l1 = reverse(l1);
        l2 = reverse(l2);
        int t = 0;
        ListNode* dummy = new ListNode(-1), *cur = dummy;
        while(t || l1 || l2) {
            if(l1) t += l1->val, l1 = l1->next;
            if(l2) t += l2->val, l2 = l2->next;
            cur = cur->next = new ListNode(t % 10);
            t /= 10;
        }
        return reverse(dummy->next);
    }
};
```

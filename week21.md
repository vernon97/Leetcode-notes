<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-07 17:11:38
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-07 20:19:40
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week21.md
-->

# Week 21 - Leetcode 201 - 210

#### 201 - 数字范围按位与

![avatar](figs/37.jpeg)

代码就是找到那个分界线 把后面的都置0 即可(先左移再右移)

```cpp
class Solution {
public:
    int rangeBitwiseAnd(int m, int n) {
        if(m == n) return m;
        for(int i = 30; i >= 0; i--)
        {
            if(!(m >> i & 1) && (n >> i & 1))
            {
                return m >> i << i;

            }
        }
        return 0;
    }
};
```

#### 202 - 快乐数

感觉是简单模拟.jpg 

其实也是链表判断有无环的问题，但本题最后结果为1 仅有1自环的可能，如果遇到重复元素且不是1的话 一定是无限循环的；

1. 哈希版

```cpp
class Solution {
public:
    bool isHappy(int n) {
        unordered_set<int> path;
        // * 只要遇到一样的数 就证明是无限循环;
        while(n != 1)
        {
            if(path.count(n)) return false;
            path.insert(n);
            int nval = 0;
            while(n)
            {
                nval += (n % 10) * (n % 10);
                n /= 10;
            }
            n = nval;
        }
        return true;
    }
};
```

2. 快慢指针版
   
```cpp
class Solution {
publioc:
    bool isHappy(int n)
    {
        int slow = n, fast = get(n);
        while(fast != slow)
        {
            fast = get(get(fast));
            slow = get(slow);
        }
        return fast == 1;
    }
    int get(int x)
    {
        int res = 0;
        while(x)
        {
            res += (x % 10) * (x % 10);
            x /= 10;
        }
        return res;
    }
}
```


#### 203 - 移除链表元素

注意的点就在 会有连续删除的情况 所以删完一个元素不能直接指next

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        // 链表删除节点 要知道前驱结点才行, 所以要新建dummy
        ListNode* dummy = new ListNode(10086), *cur = dummy;
        dummy->next = head;
        while(cur && cur->next)
        {
            if(cur->next->val == val)
                cur->next = cur->next->next;
            else
                cur = cur->next;
        }
        return dummy->next;
    }
};
```

#### 204 - 计数质数

素数筛马上拿出来背一遍

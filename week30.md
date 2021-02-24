<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-02-24 20:06:15
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-02-24 21:36:13
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week30.md
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
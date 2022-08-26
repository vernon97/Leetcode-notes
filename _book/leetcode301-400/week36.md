<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-05-27 14:21:37
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-06-14 00:05:33
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week36.md
-->
# Week 36 - Leetcode 351 - 360

### 352 - 将数据流变为多个不相交区间

**C++中的迭代器失效问题：**
分为顺序类容器和关联类容器两种：
顺序类容器（如Vector等）: 删除元素会导致后面的元素迭代器失效，但是erase会返回新的迭代器，迭代器支持随机访问 `+ x 操作`
关联类容器（如Map Set等）:删除元素不会导致后面的元素迭代器失效，但迭代器仅支持双向访问，不支持速记访问`++, --`

本题就是用Set来维护区间，**找到第一个左端点比val大的元素** 比较两边的端点和`val`来决定是新建区间还是合并区间

```cpp
class SummaryRanges {
public:
    /** Initialize your data structure here. */
    using LL = long long;
    using PLL = pair<long, long>;

    set<PLL> S;
    static const LL INF = 1e18;
    SummaryRanges() {
        // 哨兵节点
        S.insert({-INF, -INF});
        S.insert({INF, INF});
    }
    
    void addNum(int val) {
        // 找到第一个左端点比val大的
        set<PLL>::iterator r = S.upper_bound({val, INF});
        set<PLL>::iterator l = r;
        l--;
        // 分情况
        if(l->second >= val) return;

        if(l->second == val - 1 && r->first == val + 1)
        {
            // 注意 关联式容器erase不会让迭代器失效，而顺序式容器删除会导致迭代器失效
            // 但erase方法会返回新的迭代器
            S.insert({l->first, r->second});
            S.erase(l);
            S.erase(r);
        }
        else if (l->second == val - 1)
        {
            S.insert({l->first, val});
            S.erase(l);
        }
        else if (r->first == val + 1)
        {
            S.insert({val, r->second});
            S.erase(r);
        }
        else 
        {
            S.insert({val, val});
        }
    }
    vector<vector<int>> getIntervals() {
        vector<vector<int>> res;
        for(auto p : S)
            if(p.first != -INF && p.first != INF)
                res.push_back({static_cast<int>(p.first), static_cast<int>(p.second)});
        return res;
    }
};
```

### 353 - 俄罗斯信封问题

一看就是先排序 然后最长上升子序列问题hiahia

说着就复习一下最长上升子序列问题的解法


这题除了排序一维然后 LIS即可

```cpp
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        sort(envelopes.begin(), envelopes.end());
        int n = envelopes.size(), res = 0;
        vector<int> f(n + 1);
        for(int i = 0; i < n; i++)
        {
            f[i] = 1;
            auto& e = envelopes[i];
            for(int j = 0; j < i; j++)
                if(e[0] != envelopes[j][0] && e[1] > envelopes[j][1])
                    f[i] = max(f[i], f[j] + 1);
            res = max(res, f[i]);
        }
        return res;
    }
};
```

### 355 - 设计推特

这题有两个细节注意下：

- 1. 自己也算自己的follwer
- 2. 按时间戳多路归并，直接存vector就可以（默认按顺序排序），要保存timestamp，tweetId，userId，idx(归并排序的指针)

```cpp
class Twitter {
public:
    // p.first : timestamp, p.second : 
    using PII = pair<int, int>;
    unordered_map<int, vector<PII>> tweets;
    unordered_map<int, unordered_set<int>> followers;
    int timestamp;
public:
    /** Initialize your data structure here. */
    Twitter() {
        timestamp = 0;
    }
    
    /** Compose a new tweet. */
    void postTweet(int userId, int tweetId) {
        tweets[userId].emplace_back(timestamp++, tweetId);
    }
    
    /** Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent. */
    vector<int> getNewsFeed(int userId) {
        // Heap Sort
        // timestamp,tweeter_id, user_id, cur_idx
        priority_queue<vector<int>> heap;
        vector<int> res;
        auto& followers_list = followers[userId];
        followers_list.insert(userId);
        // NOTE: 自己发的也算
        for(int f : followers_list)
        {
            auto& f_tweets_list = tweets[f];
            if(tweets[f].size())
            {
                int max_len = f_tweets_list.size() - 1;
                auto t = f_tweets_list[max_len];
                heap.push({t.first, t.second, f, max_len});
            }
        }
        for(int i = 0; i < 10 && heap.size(); i++)
        {
            auto info = heap.top(); heap.pop();
            int tid = info[1], uid = info[2], cur_idx = info[3];
            res.push_back(tid);
            cur_idx--;
            if(cur_idx >= 0)
            {
                auto new_tweet = tweets[uid][cur_idx];
                heap.push({new_tweet.first, new_tweet.second, uid, cur_idx});
            }
        }
        return res;
    }
    
    /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
    void follow(int followerId, int followeeId) {
        followers[followerId].insert(followeeId);
    }
    
    /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
    void unfollow(int followerId, int followeeId) {
        followers[followerId].erase(followeeId);
    }
};

/**
 * Your Twitter object will be instantiated and called as such:
 * Twitter* obj = new Twitter();
 * obj->postTweet(userId,tweetId);
 * vector<int> param_2 = obj->getNewsFeed(userId);
 * obj->follow(followerId,followeeId);
 * obj->unfollow(followerId,followeeId);
 */
```

### 356 - 计算各个位数不相同的数字个数


总得来说是一个排列组合问题， 枚举每一位出现的可能性即可；

额外要注意的是不能有前导0（但是只有0可以）

所以举例，5位数：`9 * 9 * 8 * 7 * 6` 最后累加到一起即可


```cpp
class Solution {
public:
    int countNumbersWithUniqueDigits(int n) {
        int res = 1;
        n = min(n, 10);
        if(!n) return 1;
        for(int i = 1, j = 9, last = 9; i <= n; i++, j--)
        {
            res += last;
            last = last * j;
        }
        return res;
    }
};
```
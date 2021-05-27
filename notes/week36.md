<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-05-27 14:21:37
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-05-27 14:22:16
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week36.md
-->

### 352 - 将数据流变为多个不相交区间

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
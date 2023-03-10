# Week 44 - Leetcode 431 - 440

### 433 - 最小基因变化

比较基础的最短路问题

1. 以单词连通性建模 + 最短路
```cpp
class Solution {
public:
    int res = 9;
    int f[12][12];
    int check(string& a, string& b) {
        int cnt = 0;
        for(int i = 0; i < 8; i++)
            if(a[i] != b[i]) cnt++;
        return cnt == 1;
    }
    int minMutation(string _startGene, string _endGene, vector<string>& _bank) {
        if(find(_bank.begin(), _bank.end(), _endGene) == _bank.end()) return -1;
        int n = _bank.size();
        int loc = find(_bank.begin(), _bank.end(), _endGene) - _bank.begin();
        memset(f, 0x3f, sizeof f);
        f[0][0] = 0;
        for(int i = 1; i <= n; i++)
            if(check(_startGene, _bank[i - 1])) f[0][i] = f[i][0] = 1;
        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= i; j++){
                if(i != j && check(_bank[i - 1], _bank[j - 1])) f[i][j] = f[j][i] = 1;
                if(i == j) f[i][j] = 0;
            }
        // // bellman_ford
        for(int k = 0; k <= n; k++)
            for(int i = 0; i <= n; i++)
                for(int j = 0; j <= n; j++) {
                    f[i][j] = min(f[i][j], f[i][k] + f[k][j]);
                }
        return f[0][loc + 1] > 9 ? -1 : f[0][loc + 1];
    }
};
```

2. 基础的 BFS

```cpp
class Solution {
public:
    int res = 9;
    unordered_set<string> s;
    char chrs[4] = {'A', 'T', 'C', 'G'};
    int minMutation(string _startGene, string _endGene, vector<string>& _bank) {
        for(auto& gene : _bank) s.insert(gene);
        unordered_map<string, int> dist;
        dist[_startGene] = 0;
        queue<string> q;
        q.push(_startGene);
        while(q.size()) {
            string top = q.front();
            q.pop();
            for(int i = 0; i < 8; i++){
                auto gene = top;
                for(char c : chrs) {
                    gene[i] = c;
                    if(s.count(gene) && !dist.count(gene)){
                    dist[gene] = dist[top] + 1;
                    if(gene == _endGene) return dist[gene];
                    q.push(gene);
                    }
                }
            }
        }
        return -1;
    }
};
```

### 434 - 字符串中的单词数

按空格分割字符串, 可以使用stringstream

```cpp
class Solution {
public:
    int countSegments(string s) {
        stringstream ss{s};
        int res = 0;
        string c;
        while(ss >> c) res++;
        return res;
    }
};
```

### 435 - 无重叠区间

区间问题整体复习一下吧 不过大概的思想:

- 区间合并/区间分组：左端点排序
- 区间选点/最大不重叠区间：右端点排序


```cpp
class Solution {
public:
    static const int N = 1e5 + 10;
    struct Interval{
        int l, r;
        bool operator<(const Interval& w) const {
            return r < w.r;
        }}f[N];
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        int n = intervals.size(), idx = 0, res = 0, ed = -2e9;
        for(auto& v : intervals){
            f[idx++] = {v[0], v[1]};
        }
        sort(f, f + n);
        for(int i = 0; i < n; i++) {
            int l = f[i].l, r = f[i].r;
            if(ed > l) {
                // cout << ed << ' ' << l << endl;
                continue;
            }
            else {
                // cout << r << endl;
                ed = r;
                res ++;
            }
        }
        return n - res;
    }
};
```

### 436 - 寻找右区间

看起来复杂，实际上就是给定一个右端点，查找最近的左端点，实际上是二分查找；

二分查找就要排序，但本题是要维护原有index，可以直接在interval里插入就好了；

```cpp
class Solution {
public:
    vector<int> findRightInterval(vector<vector<int>>& intervals) {
        int n = intervals.size();
        for(int i = 0; i < n; i++) intervals[i].push_back(i);
        sort(intervals.begin(), intervals.end());
        vector<int> res(n, -1);
        for(int i = 0; i < n; i++) {
            auto& iv = intervals[i];
            int iv_left = iv[0], iv_right = iv[1], iv_idx = iv[2];
            int l = 0, r = n - 1;
            while(l < r) {
                int mid = l + r >> 1;
                if(intervals[mid][0] >= iv_right) r = mid;
                else l = mid + 1;
            }
            if(intervals[r][0] >= iv_right) res[iv_idx] = intervals[r][2];
        }
        return res;
    }
};
```

### 438 - 找到字符串中所有字母异位词

滑动窗口，注意下成功的判断即可

```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> res;
        if(p.size() > s.size()) return res;
        unordered_map<char, int> hs, hp;
        for(char c : p) hp[c]++;
        int suc = hp.size();
        for(int i = 0, j = 0; i < s.size(); i++) {
            hs[s[i]]++;
            if(hp[s[i]] > 0 && hs[s[i]] == hp[s[i]]) suc--;
            if(suc == 0) res.push_back(j);
            if(i - j + 2 > p.size()) {
                char c = s[j++];
                if(hs[c] == hp[c]) suc++;
                hs[c]--;
            }
        }
        return res;
    }
};
```

### 440 - 字典序的第K小数字

数位统计类型-按位枚举

calculate(prefix, n); 以指定的前缀prefix，直到n一共有多少种数字；

如果种类大于k，则最终的答案不是以这个prefix开头的，找下一个（prefix ++），并在k里面去掉原prefix对应的种类数sz

如果种类小于k，则最终的答案一定是以这个prefix开头的，往后补一个0继续向后查找即可（这里指定的prefix自己也算一个，所以要k--

calculate函数也是按位枚举的思想，对应的prefix后面拼接一位，种类就是0-9共十种，两位就是0-99共一百种，以此类推

直到再拼一位就超过n了的话，这里有两种可能，我举个例子：

20 - 29 对应n = 37
30 - 37 对应n = 37

所以要判断当前prefix和n的关系，决定是加 n - t + 1 还是加k；

```cpp
class Solution {
public:
    int calculate(int prefix, int n) {
        long long res = 0, t = prefix, k = 1;
        while(t * 10 <= n) {
            res += k;
            t *= 10;
            k *= 10;
        }
        if(n - t < k) res += n - t + 1;
        else res += k;
        return res;
    }
    int findKthNumber(int n, int k) {
        int prefix = 1;
        while(k > 1) {
            int sz = calculate(prefix, n);
            if(k > sz){
                prefix++;
                k -= sz;
            } else {
                prefix = prefix * 10;
                k--;
            }
        }
        return prefix;

    }
};
```
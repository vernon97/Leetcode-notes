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

区间问题整体复习一下吧 不过大概的思想都是按照左端点排序


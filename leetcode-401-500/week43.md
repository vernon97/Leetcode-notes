# Week 43 - Leetcode 421 - 430

### 421 - 数组中两个数的最大异或值

很经典的一道题先想做法再优化

方法：

- 从前到后枚举每个元素，考虑前i - 1个中哪个数异或值最大

优化：

- 如何找到？ -> trie树

复习一下trie树的建树和查询，对于本题就是查询对应位的反是否存在，然后注意下只有一个元素的边界情况即可；

```cpp
class Solution {
public:
    static const int N = 2e5 * 32 + 10;
    int son[N][2]; // trie树 只有两种
    int idx = 0;
public:
    void insert(int val) {
        int p = 0;
        for(int i = 30; i >= 0; i--) {
            int x = val >> i & 1;
            if(!son[p][x]) son[p][x] = ++idx;
            p = son[p][x];
        }
    }
    int query(int val) {
        int p = 0, res = 0;
        for(int i = 30; i >= 0; i--) {
            int x = val >> i & 1;
            if(son[p][!x]) res = res * 2 + !x, p = son[p][!x];
            else if(son[p][x]) res = res * 2 + x, p = son[p][x];
            else return 0;
        }
        return val ^ res;
    }
    int findMaximumXOR(vector<int>& nums) {
        int res = 0;
        for(int x : nums) {
            res = max(res, query(x));
            insert(x);
        }
        return res;
    }
};
```

### 423 - 从英文中重建数字

首先，任给一个符合要求的字符串，可以保证答案唯一的话，那么可以通过查找的顺序保证唯一确定；

举例来说，zero,one,two,three,four,five,six,seven,eight,nine 之中

例如eight，可以将其排在查找的第一个，因为只有他唯一拥有g这个字符，以此类推
最终可以得到一个拓扑序（不唯一），按照这个查找即可；

注意下答案要求输出字典序，所以要sort一下

```cpp
class Solution {
public:
    string originalDigits(string s) {
        vector<string> alphabet{"zero","one", "two", "three", "four", "five", "six", "seven", "eight", "nine"};
        vector<int> idx{0,8,6,7,3,2,5,4,1,9};
        unordered_map<char, int> hash;
        for(char c : s) {
            hash[c]++;
        }
        stringstream ss;
        for(int i : idx) {
            string word = alphabet[i];
            int cnt = INT_MAX;
            for(char c : word) cnt = min(cnt, hash[c]);
            for(char c : word) hash[c] -= cnt;
            while(cnt--) ss << i;
        }
        string res = ss.str();
        sort(res.begin(), res.end());
        return res;
    }
};
```

### 424 - 替换后的最长重复字符

首先，枚举替换的字符，26*

之后就变成了一个双指针问题，判断区间更新是否单调之后便可使用双指针

这里双指针的区间即为区间中有多少个字符不是枚举替换的；顺带复习一下双指针模板

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        int res = 0;
        for(char c = 'A';c <= 'Z'; c++) {
            // c 枚举改的字符数
            for(int i = 0, j = 0, cnt = 0; i < s.size(); i++) {
                if(s[i] == c) cnt++;
                while(j < i && i - j + 1 > cnt + k) cnt -= (s[j++] == c);
                res = max(res, i - j + 1);
            }
        }
        return res;
    }
};
```

### 427 - 建立四叉树

正常的递归，要注意的点有两个：

1. 如何快速判断给定区间是否为全0/全1 -> 二维前缀和

2. 划分区间注意边界点，这里注意点不能用重（+1的操作），具体边界可以给定case往里带来验证

```cpp
class Solution {
public:
    vector<vector<int>> f;
    Node* construct(vector<vector<int>>& grid) {
        int n = grid.size();
        f = vector<vector<int>>(n + 1, vector<int>(n + 1));
        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= n; j++) {
                f[i][j] = f[i - 1][j] + f[i][j - 1] - f[i - 1][j - 1] + grid[i - 1][j - 1];
            }
        return dfs(1, 1, n, n);
    }
    Node* dfs(int si, int sj, int ei, int ej) {
        // 判断是否为纯1 / 纯0 -> 前缀和
        Node* res = new Node();
        int ans = f[ei][ej] - f[si - 1][ej] - f[ei][sj - 1] + f[si - 1][sj - 1];
        if(ans == 0 || ans == (ei - si + 1) * (ej - sj + 1)) {
            res->val = ans > 0;
            res->isLeaf = true;
            return res;
        }
        res->topLeft = dfs(si, sj, (si + ei) / 2, (sj + ej) / 2);
        res->bottomRight = dfs((si + ei) / 2 + 1, (sj + ej) / 2 + 1, ei, ej);
        res->topRight = dfs(si, (sj + ej) / 2 + 1, (si + ei) / 2, ej);
        res->bottomLeft = dfs((si + ei) / 2 + 1, sj, ei, (sj + ej) / 2);
        return res;
    }
};
```

### 429 - n叉树的层序遍历

本质上和二叉树的层序遍历没有本质区别，都是BFS

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        vector<vector<int>> res;
        if(root == nullptr) return res;
        queue<Node*> q;
        q.push(root);
        while(q.size()) {
            vector<int> level;
            int len = q.size();
            while(len--) {
                auto t = q.front();
                q.pop();
                level.push_back(t->val);
                for(auto x : t->children) q.push(x);
            }
            res.push_back(move(level));
        }
        return res;
    }
};
```

### 430 - 扁平化多级双向链表

这里的递归要学习下，包括返回值怎么设计，if(cur->prev) 判空等等；
具体逻辑就画个图吧~

```cpp
class Solution {
public:
    Node* flatten(Node* head) {
        auto res = dfs(head);
        return res[0];
    }
    vector<Node*> dfs(Node* head) {
        if(head == nullptr) return {nullptr, nullptr};
        Node* cur = head, *tail = head;
        while(cur) {
            tail = cur;
            if(cur->child) {
                auto res = dfs(cur->child);
                cur->child = nullptr;
                res[0]->prev = cur;
                res[1]->next = cur->next;
                if(cur->next) cur->next->prev = res[1];
                cur->next = res[0];
                cur = res[1]->next;
                tail = res[1];
            } else {
                cur = cur->next;
            }
        }
        return {head, tail};
    }
};
```


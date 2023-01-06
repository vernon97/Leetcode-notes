# Week 39 - Leetcode 381 - 390

### 381 - O(1)时间插入，删除和获取随机元素 - 允许重复

和上一题比起来，需要有重复元素，依旧是哈希表和数组的结合；

但是本题允许重复元素，所以哈希表1套哈希表2，哈希表2存储该元素的所有index；

- O(1)插入:正常操作
- O(1)删除：
    - 思路还是一样的，但是x出现的位置不一定只有一次；
    - 所以数组交换，对应哈希表交换也是类似的，但是由于嵌套要稍作修改；

另外要注意的情况是可能存在阐述元素和arr.back()是同一个元素的情况，这里通过一点技巧来解决

```cpp
class RandomizedCollection {
public:
    vector<int> arr;
    unordered_map<int, unordered_set<int> > h;
public:
    RandomizedCollection() {
    }
    
    bool insert(int val) {
        bool res = h[val].empty();
        h[val].insert(arr.size());
        arr.push_back(val);
        return res;
    }
    
    bool remove(int x) {
        if(h[x].size())
        {
            int y = arr.back();
            int px = *h[x].begin(), py = arr.size() - 1;
            swap(arr[px], arr[py]);
            h[x].erase(px), h[x].insert(py);
            h[y].erase(py), h[y].insert(px);
            h[x].erase(py);
            arr.pop_back();
            return true;
        }
        return false;

    }
    
    int getRandom() {
        return arr[rand() % arr.size()];
    }
};
```

### 382 - 链表随机节点

如果不限制空间复杂度`O(1)`的话，和380这题是一样的，搞个vector存就可以了；

但本题要求的是空间复杂度`O(1)`，对于这种不知道长度的等概率返回问题，有经典算法，背过就好了，叫**蓄水池算法**

大概的含义是：设定一个最终选择的元素X, 从前到后遍历，第i个元素有`1/i`的概率替换最终选择的元素X；

具体的说，第一个元素有100%的概率替换X, 第二个元素50%，第三个元素1/3，以此类推

这样，第K个元素最终被选中，需要同时满足在遍历到它的时候被选中 + 后面所有的元素都没被替换，用概率表示如下：

`1/k * (1 - k/(k+1)) * (1 - (k+1)/(k+2) .... * (1 - (n - 1)/n)`

分子分母相消，最终刚好是1/n

```cpp
class Solution {
public:
    ListNode* h;
public:
    Solution(ListNode* head) {
        h = head;
    }
    
    int getRandom() {
        int res = INT_MAX, n = 1;
        for(auto p = h; p; p = p->next, n++) {
            if(rand() % n == 0) res = p->val;
        }
        return res;
    }
};
```

### 383 - 赎金信

哈希表统计下各个字母出现次数就行啦~

```cpp
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        vector<int> a(26,0);
        for(int i = 0; i < ransomNote.size() || i < magazine.size(); i++)
        {
            if(i < ransomNote.size()) a[ransomNote[i] - 'a']--;
            if(i < magazine.size()) a[magazine[i] - 'a']++;
        }
        for(int i = 0; i < 26; i++) {
            if(a[i] < 0) return false;
        }
        return true;

    }
};
```

### 384 - 打乱数组

C++之中是有接口的，`random_shuffle()`, 但这种序列随机的可以一个一个元素考虑；

我们从前到后，随机选一个当前元素i后面的元素[i, n]，和其swap一下；

第一个元素有n种可能，第二个元素有n-1种可能，刚好满足穷举+等概率；

```cpp
class Solution {
public:
    int n;
    vector<int> origin;
public:
    Solution(vector<int>& nums) {
        n = nums.size();
        origin = vector<int>(nums);
    }
    
    vector<int> reset() {
        return origin;
    }
    
    vector<int> shuffle() {
        vector<int>res{origin};
        for(int i = 0; i < n; i++) {
            swap(res[i], res[i + rand() % (n - i)]);
        }
        return res;

    }
};
```

### 385 - 迷你语法分析器

这个递归还挺难想的 多写几次吧

```cpp
class Solution {
public:
NestedInteger deserialize(string s)
{
    int u = 0;
    return dfs(s, u);
}
NestedInteger dfs(string& s, int& u) {
    NestedInteger res;
    if(s[u] == '[') {
        u++;
        while(s[u] != ']') res.add(dfs(s, u));
        u++;
        if(u < s.size() && s[u] == ',') u++;
    } 
    else  // 当前是数字
    {
        int k = u;
        while (k < s.size() && s[k] != ',' && s[k] != ']') k++;
        res.setInteger(stoi(s.substr(u, k - u)));
        if (k < s.size() && s[k] == ',') k++; // 跳过逗号
        u = k;
    }
    return res;
}
};
```

### 386 - 字典序排数

trie数的思想 + dfs

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> lexicalOrder(int n) {
        for(int i = 1; i <= 9; i++)
            dfs(i, n);
        return res;
    }
    void dfs(int cur, int n) {
        if(cur <= n) res.push_back(cur);
        else return;
        for(int i = 0; i <= 9; i++) dfs(cur * 10 + i, n);
    }

};
```

### 387 - 字符串中的第一个唯一字符

这个比较简单，遍历两次即可

```cpp
class Solution {
public:
    int firstUniqChar(string s) {
        vector<int> v(26, 0);
        for(auto c : s) {
            v[c - 'a'] ++;
        }
        for(int i = 0; i < s.size(); i++) {
            if(v[s[i] - 'a'] == 1) return i;
        }
        return -1;
    }
};
```

### 388 - 文件的最长绝对路径

给定一个树的遍历形式，找到最长路径 -> 这里用栈来实现，用栈的size和\t的个数来维护深度信息

```cpp
class Solution {
public:
    int lengthLongestPath(string input) {
        stack<int> stk;
        int res = 0;
        for(int i = 0, sum = 0; i < input.size(); i ++) {
            int k = 0;
            while(i < input.size() && input[i] == '\t') i++, k++;
            while(k < stk.size()) sum -= stk.top(), stk.pop();
            int j = i;
            while(j < input.size() && input[j] != '\n') j++;
            stk.push(j - i);
            sum += j - i;
            if(input.substr(i, j - i).find('.') != -1)
                res = max(res, sum + static_cast<int>(stk.size()) - 1);
            i = j;
        }
        return res;
    }

};
```

### 389 - 找不同

这个比较简单，字符串也比较短，自定义一个hash函数计算字符串的数值和就可以了

```cpp
class Solution {
public:
    int hash(string& s) {
        int val = 0;
        for(char c : s) {
            val += c - 'a';
        }
        return val;
    }
    char findTheDifference(string s, string t) {
        return hash(t) - hash(s) + 'a';
    }
};
```

### 390 - 消除游戏

经典的约瑟夫问题，都是DP解法；

1 - n 从左往右删的结果, 记作 f(n)
1 - n 从右往左删的结果, 记作 g(n)

对于从左往右删的结果，f(n) = 2 * g(n / 2)
对于f(n)和g(n)而言，从左往右删的结果和从右往左删的结果应该是中心对称的，因此f(n) + g(n) = n + 1 (这里可以举几个例子验证一下)

因此得到dp的递推式，记忆化搜索可以得到；

```cpp
class Solution {
public:
    int lastRemaining(int n) {
        // f(n) = 2 * (n / 2 + 1 - f(n / 2))
        if(n == 1) return 1;
        else return 2 * (n / 2 + 1 - lastRemaining(n / 2));
    }
};
```



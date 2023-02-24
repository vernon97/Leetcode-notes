# Week 41 - Leetcode 401 - 410

### 401 - 二进制手表

枚举所有的二进制可能性，如果满足二进制位数的话，保存对应的小时与分钟；

用到的几个知识点：
- 计算二进制有多少个1，lowbit运算 i & (i - 1)
- sprintf 与 char[]格式化输出应用

```cpp
class Solution {
public:
    vector<string> res;
public:
    int count(int i) {
        int cnt = 0;
        while(i > 0){
            i = i & (i - 1);
            cnt++;
        }
        return cnt;
    }
    vector<string> readBinaryWatch(int turnedOn) {
        char str[10];
        for(int i = 0; i < 1 << 10; i ++) {
            if(count(i) == turnedOn) {
                int hour = i >> 6, minute = i & ((1 << 6) - 1);
                if(hour < 12 && minute < 60) {
                    sprintf(str, "%d:%02d", hour, minute);
                    res.push_back(str);
                }
            }
        }
        return res;
    }
};
```

### 402 - 移掉k位数字

是贪心问题，解决框架如下：

1）如何枚举所有情况

2）对于每种情况是否有确定的最优选择

对于本题，枚举所有情况通过对于每一位s_i, 判断他要不要删除；
而对于删除与否，最优解在于和前一位s_i-1的关系：

- 如果s_i > s_i - 1，则最后生成的序列前面都是一样的，删除s_i一定比删除s_i - 1好；反过来则是删除s_i - 1更好；

而相等时候，将选择权交给后面的元素，所以选择不删；

这样，最后得到的答案一定是递增序列，如果还有能多删除的元素的话，从后往前删；

最后记得答案中处理下前导零；

```cpp
class Solution {
public:
    string removeKdigits(string num, int k) {
        k = min(k, static_cast<int>(num.size()));
        string res;
        for(char c : num) {
            while(k && res.size() && res.back() > c) {
                res.pop_back();
                k--;
            }
            res += c;
        }
        while(k--) {res.pop_back();}
        //处理前导0;
        k = 0;
        while(k < num.size() && res[k] == '0') k++;
        if(k == res.size()) res += '0';
        return res.substr(k);

    }
};
```

### 403 - 青蛙过河


状态定义：f[i, j] 表示目前在i节点，从上一步跳j而来，这样状态转移方程就是枚举前面的位置，看看能否转移过来；

初始状态 f[0][0] 为真，额外要注意的是k一定验证小于j + 1 不然bool数组会爆炸；
```cpp
class Solution {
public:
    static const int N = 2020;
    bool f[N][N];
public:
    bool canCross(vector<int>& stones) {
        int n = stones.size();
        // 状态定义 f[i][j] 表示当前抵达i, 上一次跳了j步而来的方案是否可行
        f[0][0] = true;
        for(int i = 1; i < n; i++) {
            for(int j = 0; j < i; j++) {
                int k = stones[i] - stones[j];
                // 重点在这
                if(k <= j + 1) {
                    f[i][k] = f[j][k - 1] || f[j][k] || f[j][k + 1];
                }
            }
        }
        for(int i = 0; i < n; i++)
            if(f[n - 1][i]) return true;
        return false;
    }
};
```

### 404 - 左叶子之和

额外判断下左节点是不是叶子节点即可（左右儿子都为nullptr)

```cpp
class Solution {
public:
    int res = 0;
public:
    int sumOfLeftLeaves(TreeNode* root) {
        dfs(root, false);
        return res;
    }
    void dfs(TreeNode* p, bool isLeft) {
        if(p == nullptr) return;
        if(p->left == nullptr && p->right == nullptr && isLeft) res += p->val;
        dfs(p->left, true);
        dfs(p->right, false);
    }
};
```

### 405 - 数字转换为16进制

首先，负数的问题是不用管的，int存的时候也是按照补码存的，所以直接转为unsigned int即可；

这样每四位一组 对应一个十六进制位 取出来用&运算，并依次右移四位即可

```cpp
class Solution {
public:
    string toHex(unsigned int num) {
        if(num == 0) return "0";
        string snip = "0123456789abcdef";
        string res = "";
        while(num) {
            res += snip[num & 0b1111];
            num = num >> 4;
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```

### 407 - 接雨水II

to be continued


### 409 - 最长回文串

除去中心字符（有 or 无都可）剩下的元素对要出现两次；
这样就调出所有出现两次倍的元素，如果有剩下的就随便放一个元素在中心

除以二下取整有很多方式 除法 / 取余 或者像本题一样的直接位运算扣掉最后的1

```cpp
class Solution {
public:
    int longestPalindrome(string s) {
        unordered_map<char, int> h;
        for(char c : s) {
            h[c]++;
        }
        int res = 0, maxodd = 0;
        for(auto& p : h) {
            int val = p.second;
            res += val & 0xfffffffe;
        }
        if(res < s.size()) res++;
        return res;
    }
};
```

### 410 - 分割数组的最大值

首先，对于一个给定的mid值， 可以最少分多少段？

-> 这是个贪心解：因为是非负整数 + 连续分割，所以从前到后遍历，能放前一段不超过mid的话，就放，否则就开新的一段；

这样的话，给定段数就可以二分mid值来判断；

```cpp
class Solution {
public:
    vector<int> nums;
    int k;
public:
    int splitArray(vector<int>& _nums, int _k) {
        nums = _nums;
        k = _k;
        int l = 0, r = INT_MAX;
        while(l < r) {
            int mid = (l + r) >> 1;
            if(check(mid)) r = mid;
            else l = mid + 1;
        }
        return r;
    }
    // 这里的check 指给定了mid 贪心判断最少能分几段
    bool check(int mid) {
        int res = 0, addr = 0;
        for(int i = 0; i < nums.size(); i++) {
            if(addr + nums[i] <= mid) addr += nums[i];
            else if (nums[i] > mid) return false;
            else {
                res++;
                addr = nums[i];
            }
        }
        if(addr > 0) res++;
        return res <= k; 
    }
};
```
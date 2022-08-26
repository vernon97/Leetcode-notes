# Week 02 - Leetcode 11 - 20

#### 11 - 盛最多水的容器

**做法： 双指针扫描 哪个短指针就向内移动一个单位 并更新最大体积**

证明：

设每一状态下水槽面积为 $S (i, j) (0<=i\<j\<n)$，由于水槽的实际高度由两板中的短板决定，则可得面积公式 $S(i,j)=min(h\[i],h\[j])×(j−i)$.

在每一个状态下，无论长板或短板收窄 1 格，都会导致水槽 底边宽度 − 1 ： 若向内移动短板，水槽的短板 $min(h\[i],h\[j])$ 可能变大，因此水槽面积 $S(i,j) $可能增大。 若向内移动长板，水槽的短板 $min(h\[i],h\[j])$不变或变小，下个水槽的面积一定小于当前水槽面积。

以上保证我们一定可以遍历到最优解；

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1, res = 0;
        while(l < r)
        {
            int hl = height[l], hr = height[r];
            res = max(res, (r - l) * min(hl, hr));
            if(hl < hr)
                l++;
            else
                r--;
        }
        return res;
    }
};
```

#### 12 - 整数转罗马数字

感觉就是模拟诶。。就嗯打表；

这里频繁拼接字符串要记得用stringstream, 别硬+=；

```cpp
class Solution {
public:
    string intToRoman(int num) {
        map<int, string, greater<int>> dict = {
            {1000, "M"},
            {900, "CM"},
            {500, "D"},
            {400, "CD"},
            {100, "C"},
            {90, "XC"},
            {50, "L"},
            {40, "XL"},
            {10, "X"},
            {9, "IX"},
            {5, "V"},
            {4, "IV"},
            {1, "I"}
        };
        stringstream ss;
        for(auto& p : dict)
        {
            int base = p.first;
            string val = p.second;
            while(num >= base)
            {
                ss << val;
                num -= base;
            }
        }
        return ss.str();
    }
};
```

```cpp
class Solution {
public:
    string intToRoman(int num) {
        string val[] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        int base[] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        stringstream ss;
        for(int i = 0; i < 13; i++)
        {
            int b = base[i];
            string v = val[i];
            while(num >= b)
            {
                ss << v;
                num -= b;
            }
        }
        return ss.str();
    }
};
```

#### 13 - 罗马数字转整数

依旧是模拟就完事了 注意一下两个字符连接的特殊情况；

```cpp
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char, int> dict = {
            {'M' , 1000},
            {'D' , 500},
            {'C' , 100},   
            {'L' , 50},
            {'X' , 10},   
            {'V' , 5}, 
            {'I' , 1}
        };
        unordered_set<char> conflicts = {'C', 'X', 'I'};
        unordered_map<string, int> two_dicts = 
        {
            {"CM", 900},
            {"CD", 400},
            {"XC", 90},
            {"XL", 40},
            {"IX", 9},
            {"IV", 4}
        };
        int i = 0, res = 0;
        while(i < s.size())
        {
            // 有碰撞可能的就是 I, X, C;
            char cur = s[i];
            if(conflicts.count(cur) && i + 1 < s.size())
            {
                string two = s.substr(i, 2);
                if(two_dicts.count(two))
                {
                    res += two_dicts[two];
                    i += 2;
                    continue;
                }
            }
            res += dict[cur];
            i++;
        }
        return res;
    }
};
```

相比于暴力枚举 这里可以找规律在于 例如IX, CD 这种情况的出现 I 表示的数字一定小于X;

也就是说只要左边的字母小于右边的字母， 那么左边的字母表示的就是负值；

> _**NOTE:**_ IX = 5 - 1; XC = 100 - 10; ...

```cpp
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char, int> hash = 
        {
            {'I', 1},
            {'V', 5},
            {'X', 10},
            {'L', 50},
            {'C', 100},
            {'D', 500},
            {'M', 1000}
        };
        int res = 0, i = 0;
        while(i < s.size())
        {
            if(i + 1 < s.size() && hash[s[i]] < hash[s[i + 1]])
                res -= hash[s[i]];
            else
                res += hash[s[i]];
            i++;
        }
        return res;
    }
};
```

#### 14 - 最长公共前缀

这个就是拿个指针往后指就好了（x 同样的 拼接多个字符串记得用stringstream

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if(strs.empty()) return "";
        if(strs.size() == 1) return strs[0];
        stringstream ss;
        int i = 0;
        while(true)
        {  
            char cur = 'A';
            for(string& s : strs)
            {
                if(cur == 'A')
                {
                    cur = s[i];
                    continue;
                }
                if(i >= s.size() || s[i] != cur)
                    return ss.str();
            }
            ss << cur;
            i++;
        }
        return "";
    }
};
```

#### 15 - 三数之和

排序 然后双指针算法来解决，这里有一些重复元素的边界问题需要小心考虑一下

重复问题主要出现在一下几个地方：

> _**NOTE:**_ 1. 枚举起点时候 a a b 在枚举前一个a 的时候就考虑到了后面的情况 同时包含了a a 同时出现的情况 这个时候不必枚举后面一个a；

> _**NOTE:**_ 2. 在得到答案的时候 a bbb xxx ccc 已经得到一个答案a b c 之后 重复的b c 都可以直接过掉；

记住这两个过滤重复元素的地方就可以了；

```cpp
class Solution {
public:
    int n;
    vector<vector<int>> res;
public:
    void search(vector<int>& nums, int target, int si)
    {
        int l = si + 1, r = n - 1;
        while(l < r)
        {
            int cur_sum = nums[l] + nums[r];
            if(cur_sum > target)
                r--;
            else if (cur_sum < target)
                l++;
            else{
                int cl = nums[l], cr = nums[r];
                res.push_back({-target, cl, cr});
                // 去重
                while(l < r && nums[l] == cl) l++;
                while(l < r && nums[r] == cr) r--;
            }
        }
    }
    vector<vector<int>> threeSum(vector<int>& nums) {
        n = nums.size();
        if(n < 3) return res;
        sort(nums.begin(), nums.end());
        // 枚举起点
        for(int i = 0; i < n - 2; i++)
        {
            if(i && nums[i - 1] == nums[i]) continue;
            search(nums, -nums[i], i);
        }
        return res;
    }
};
```

#### 16 - 最接近的三数之和

1. 直接枚举：和上一题差不多 还少了判重

```cpp
class Solution {
public:
    int n, diff, res;
public:
    void search(vector<int>& nums, int target, int si)
    {
        int l = si + 1, r = n - 1;
        while(l < r)
        {
            int cur_sum = nums[l] + nums[r];
            if(cur_sum > target)
                r--;
            else
                l++;
            if(abs(cur_sum - target) < diff)
            {
                diff = abs(cur_sum - target);
                res = cur_sum - target;
            }
        }
    }
    int threeSumClosest(vector<int>& nums, int target) {
        n = nums.size(), diff = 0x3f3f3f3f;
        sort(nums.begin(), nums.end());
        for(int i = 0; i + 2 < n; i++)
            search(nums, target - nums[i], i);
        return res + target;
    }
};
```

#### 17 -  电话号码的字母组合

基础dfs

```cpp
class Solution {
public:
    string nums[10] = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    vector<string> res;
public:
    vector<string> letterCombinations(string digits) {
        if(digits.empty()) return res;
        dfs(digits, 0, "");
        return res;
    }
    void dfs(string& digits, int u, string path)
    {
        if(u == digits.size())
            res.push_back(path);
        else
            for(const char s : nums[digits[u] - '0'])
                dfs(digits, u + 1, path + s);
    }
};
```

#### 18 - 四数之和

和三数之和一样 只不过多枚举一个然后同样的判重小心一点

这里相当于三数之和是枚举完i的子问题；

```cpp
class Solution {
public:
    vector<vector<int>> res;
    int n;
public:
    void search(vector<int>& nums, int si, int sj, int target)
    {
        int l = sj + 1, r = n - 1;
        while(l < r)
        {
            int cur_sum = nums[l] + nums[r];
            if(cur_sum > target)
                r--;
            else if (cur_sum < target)
                l++;
            else{
                int cl = nums[l], cr = nums[r];
                res.push_back({nums[si], nums[sj], cl, cr});
                while(l < r && cl == nums[l]) l++;
                while(l < r && cr == nums[r]) r--;
            }
        }
    }
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        n = nums.size();
        sort(nums.begin(), nums.end());
        for(int i = 0; i < n - 3; i++)
        {
            if(i && nums[i - 1] == nums[i]) continue;
            for(int j = i + 1; j < n - 2; j++)
            {
                if(j > i + 1 && nums[j - 1] == nums[j]) continue;
                search(nums, i, j, target - nums[i] - nums[j]);
            }
        }
        return res;
    }
};
```

#### 19 - 删除链表的倒数第N个节点

可能会删除头结点 -> 虚拟头结点 先找到倒数第k-1个节点 使其指向k+1个节点

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int k) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;

        int n = 0;
        for(auto p = dummy; p; p = p->next) n++;
        ListNode* cur = dummy;
        
        for(int i = 0; i < n - k - 1; i++)
            cur = cur->next;
        
        cur->next = cur->next->next;
        return dummy->next;
    }
};
```

#### 20 - 有效的括号

用栈来实现 可以发现ASCII码中 ( 与 ) 差1 \[ 与 ] { 与 } 差2，所以可以直接用字符差值是否在2以内判断是否匹配 这里用模拟栈会更快一点

```cpp
class Solution {
public:
    bool isValid(string s) {
        unordered_set<char> left = {'(', '[', '{'};
        // stack
        char stack[5000];
        int tt = 0;
        for(const char& c : s)
        {
            if (left.count(c))  stack[++tt] = c;
            else
            {
                if(tt && abs(stack[tt] - c) <= 2) tt--;
                else return false;
            }
        }
        return tt == 0;
    }
};
```

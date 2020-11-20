# Week 01 - Leetcode 01 - 10



#### 01 - 两数之和

哈希表记录target - nums[i] 是否出现过

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> heap;
        for(int i = 0; i < nums.size(); i++)
        {
            int r = target - nums[i];
            if(heap.count(r)) return {heap[r], i};
            heap[nums[i]] = i;
        }
        return {};
    }
}
```

#### 02 - 两数相加

对于头结点特判 可以用一个dummy head 避免

```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(-1), *cur = dummy;
        int t = 0;
        while(l1 || l2 || t) // 还有l或者还有进位标志
        {
            if(l1) t += l1->val, l1 = l1->next;
            if(l2) t += l2->val, l2 = l2->next;
            cur = cur->next = new ListNode(t % 10);
            t /= 10;
        }
        return dummy->next;
    }
};
```

#### 03 - 无重复字符的最长子串

双指针算法的应用，左指针l 右指针r； 单调性使得遍历r时 l不必每次都回溯到初始位置，保证了每个点都只被遍历一次-&gt; o(n);

证明：反证法 证明l指针只需要在上次的位置向后走

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        // 双指针
        int res = 0;
        unordered_map<char, int> heap;
        for(int l = 0, r = 0; r < s.size(); r++)
        {
            heap[s[r]]++;
            while (heap[s[r]] > 1) heap[s[l++]] --; // 逐渐删除
            res = max(res, r - l + 1);
        }  
        return res;
    }
};
```

#### 04 - 寻找两个正序数组的中位数

经典劝退题目 QAQ

奇数个数: 中位数

偶数个数: 中间两个数的平均数

nums1, nums2都是有序的；

**方法: 递归** (不要用二分去做 二分太难了)

原问题难以直接递归求解，所以我们先考虑这样一个问题：

>__Note:__ **在两个有序数组中，找出第k小数。**

如果该问题可以解决，那么第 (n+m)/2 小数就是我们要求的中位数.

先从简单情况入手，假设 m,n≥k/2，我们先从 nums1和 nums2中各取前 k/2个元素：

如果 nums1[k/2−1] <= nums2[k/2−1]，则说明 nums1中取的元素过多，nums2中取的元素过少；因此 nums2中的前 k/2个元素一定都小于等于第 k小数，所以我们可以先取出这些数，将问题归约成在剩下的数中找第 k−⌊k/2⌋小数.

如果 nums1[k/2−1] >= nums2[k/2−1]，同理可说明 nums1中的前 k/2个元素一定都小于等于第 k小数，类似可将问题的规模减少一半.

现在考虑边界情况，如果 m < k/2，则我们从 nums1中取m个元素，从nums2中取 k/2个元素（由于 k=(n+m)/2，因此 m,n不可能同时小于 k/2

如果 nums1[m−1] > nums2[k/2−1]，则 nums2中的前 k/2个元素一定都小于等于第 k小数，我们可以将问题归约成在剩下的数中找第 k−⌊k/2⌋小数.如果 nums1[m−1] <= nums2[k/2−1]，则 **nums1中的所有元素**一定都小于等于第 k小数，因此第k小数是 nums2[k−m−1].

**时间复杂度分析：** k=(m+n)/2，且每次递归k的规模都减少一半，因此时间复杂度是 O(log(m+n)).

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int total_size = nums1.size() + nums2.size();
        if(total_size & 1)
            return find(nums1, 0, nums2, 0, total_size / 2 + 1);
        else
        {
            int left  = find(nums1, 0, nums2, 0, total_size / 2);
            int right = find(nums1, 0, nums2, 0, total_size / 2 + 1);
            return (left + right) / 2.0; 
        }
    }
    int find(vector<int>& nums1, int i, vector<int>& nums2, int j, int k)
    {
        if(nums1.size()  - i > nums2.size() - j)
            return find(nums2, j, nums1, i, k);
        if(nums1.size() == i) return nums2[j + k - 1];
        if(k == 1)
            return min(nums1[i], nums2[j]);
        int si = min((int)nums1.size(), i + k / 2), sj = j + k - k / 2;
        if(nums1[si - 1] > nums2[sj - 1])
            return find(nums1, i, nums2, sj, k - (sj - j));
        else 
            return find(nums1, si, nums2, j, k - (si - i));

    }
};
```

#### 05 - 最长回文子串

马拉车算法 - o(n) 仅能做最长回文子串 就不学了

**字符串哈希 + 二分 o(nlogn)**

分别记录前序和后序的哈希值 把逐个判断是否对称转换为直接判断哈希值是否相等

回文子串有两种，偶数长度和奇数长度，为了化简代码在每两个字符内插入一个 **‘ |** ’来把所有的偶数长度回文子串转化为奇数的；

举例来说：

aba -> a|b|a

abba -> a|b|b|a

这样可能会存在|a|b|a|形如这样的回文子串问题 边界问题最后解决就好；

分别从前序和后序记录字符串哈希值 这里后序的哈希要注意下标怎么来的;

字符串哈希: base = 131或者13331, h[r] - h[l - 1] * p[r - l + 1]

 记得初始化p[0] = 1（p是用来记录base的p次幂的）

```cpp
typedef unsigned long long ULL;
class Solution {
public:
    static const int N = 2020, base = 131;
    ULL hl[N], hr[N], p[N];
    char str[N];
public:
    ULL get(ULL h[], int l, int r)
    {
        return h[r] - h[l - 1] * p[r - l + 1];
    }
    string longestPalindrome(string s) {
        // 最长回文子串 - 马拉车 类似kmp o(n)
        // 字符串哈希 + 二分 o(nlogn)
        // 回文串 (左右对称的串)
        int n = 2 * s.size(), res = 0, i_idx = 0;
        p[0] = 1;
        strcpy(str + 1, s.c_str());
        for(int i = n; i > 0; i -= 2)
        {
            str[i] = str[i / 2];
            str[i - 1] = 'a' + 27;
        }
        for(int i = 1, j = n; i <= n; i++, j--)
        {
            hl[i] = hl[i - 1] * base + str[i] - 'a' + 1;
            hr[i] = hr[i - 1] * base + str[j] - 'a' + 1;
            p[i] = p[i - 1] * base;
        }
        // 二分长度
        // 记录长度与中间位置
        for(int i = 1; i <= n; i++)
        {
            int l = 0, r = min(n - i, i - 1);
            while(l < r)
            {
                int mid = (l + r + 1) >> 1;
                if(get(hl, i - mid, i - 1) != get(hr, n - (i + mid) + 1, n - (i + 1) + 1))
                    r = mid - 1;
                else
                    l = mid; 
            }
            // 判断原始长度(未加27)
            if(str[i - l] <= 'z')
                l ++;
            if(res < l)
            {
                res = l;
                i_idx = i;
            }
        }
        return s.substr((i_idx - res) / 2, res);
    }
};
```

#### 06 - Z字形变换

等差数列找规律的问题 注意开一个字符串数组 别用+

```cpp
class Solution {
public:
    string convert(string s, int k) {
        int n = s.size(), cnt = 0;
        char str[n + 1];
        if(k == 1) return s;
        for(int i = 0; i < k; i++)
        {
            if(i == 0 || i == k - 1)
            {
                for(int j = i; j < n; j += 2 * k - 2)
                {
                    str[cnt++] = s[j];
                }
            }
            else 
            {
                for(int j = i, m = 2 * k - 2 - i; j < n || m < n; j += 2 *k - 2, m += 2 * k - 2)
                {
                    if(j < n) str[cnt++] = s[j];
                    if(m < n) str[cnt++] = s[m];
                }
            }
        }
        str[cnt] = '0';
        return string(str);
    }
};
```

#### 07 - 整数反转

注意的点在于 -4 % 10 = -4 （在c++中) 而python和数学的定义都是等于6的

所以下面的代码可以同时处理正数和负数的情况；

```cpp
class Solution {
public:
    int reverse(int x) {
        // 溢出怎么搞?
        long long res = 0;
        while(x)
        {
            res = res * 10 + x % 10;
            x /= 10;
        }
        if(res > INT_MAX || res < INT_MIN)
            return 0;
        else
            return res;
    }
};
```

#### 08 - 实现atoi

模拟 判断是否为数字 isdigit()

```cpp
class Solution {
public:
    int myAtoi(string s) {
        int k = 0;
        // 1. 去掉字符串首的空格
        while(k < s.size() && s[k] == ' ')
            k++;
        if(k == s.size()) return 0;
        //2. 处理符号
        int minus = 1;
        if(s[k] == '-')
        {
            minus = -1;
            k++;
        }
        else if(s[k] == '+')
            k++;
        long long res = 0;
        // 直接处理数字
        while(k < s.size() && isdigit(s[k]))
        {
            res = res * 10 + s[k] - '0';
            if(res > INT_MAX) break;
            k++;
        }
        res = res * minus;
        if(res > INT_MAX) return INT_MAX;
        if(res < INT_MIN) return INT_MIN;
        return res;
    }
};
```

#### 09 - 回文数

1. 字符串方法

记一下cpp中如何反转字符串

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if(x < 0)
            return false;
        string s = to_string(x); // int转str
        return s == string(s.rbegin(), s.rend());
    }
};
```

2. 数字方法

 **​**   和整数反转的方法类似

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        int x_cpy = x;
        if(x < 0)
            return false;
        long long x_reverse = 0;
        while(x_cpy)
        {
            x_reverse = x_reverse * 10 + x_cpy % 10;
            x_cpy /= 10;
        }
        //cout << x << ' ' << x_reverse << endl;
        return x_reverse == x;
    }
};
```

#### 10 - 正则表达式匹配

实际上是动态规划问题 （好难）

状态表示 **f(i, j) 表示所有s(1 - i) 和p(1 - j) 的匹配方案， 值是一个bool值 表示是否存在一个合法方案。**

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        s = ' ' + s, p = ' ' + p;
    //    vector<vector<bool>> f(n + 1, vector<bool>(m + 1));
        bool f[n + 1][m + 1];
        memset(f, false, sizeof f);
        f[0][0] = true;
        for(int i = 0; i <= n; i++)
            for(int j = 1; j <= m; j++)
            {
                if(j + 1 <= m && p[j + 1] == '*') continue;
                if(i && p[j] != '*')
                    f[i][j] = f[i - 1][j - 1] && (s[i] == p[j] || p[j] == '.');
                else if (p[j] == '*')
                    f[i][j] = f[i][j - 2] || (i && f[i - 1][j] && (s[i] == p[j - 1] || p[j - 1] == '.')); 
            } 
        return f[n][m];
    }
};
```



设状态 f(i,j) 表示字符串 s 的前 i 个字符和字符串 p 的前 j 个字符能否匹配.

这里假设 s 和 p 的下标均从 1 开始。初始时，f(0,0)=true 

**转移1 - p(j)不为\*:**  f(i, j)=f(i, j) or f(i−1, j−1) ，当 i>0 且 s(i) == p(j) || p(j) == '.'

**转移2 - p(j)为\*:**  若 j>=2 ，f(i,j) 可以从 f(i,j−2) 转移，表示丢弃这一次的 '\*‘ 和它之前的那个字符；

若 i>0 且 s(i) == p(j - 1)，表示这个字符可以利用这个 '\*'，则可以从 f(i−1, j) 转移，表示利用 '\*’ 来代替前面的字符；


利用这个， 就可以枚举\*用来表示多少个字符， 有一种情况成立即可:
所以在f(i - 1, j - 2) && si, f(i - 2, j - 2) && si && si - 1, f( i - 3, j - 2 ) && si && si-1 && si-2.. 中只要有一个满足即可
在这里我们注意到：

**f(i, j) = f(i, j - 2) || f(i - 1, j - 2) && si || f(i - 2, j - 2) && si && si - 1||f( i - 3, j - 2 ) && si && si-1 && si-2..**

**f(i - 1, j) = f(i - 1, j - 2)||f(i - 2, j - 2)  && si - 1||f( i - 3, j - 2 ) && si-1 && si-2..**

和完全背包的优化方式一样 **f(i, j) = f(i, j - 2) ||(si == p && f(i - 1, j)**

初始状态f(0,0)=true ；循环枚举 i 从 0 到 n ；j 从 1 到 m 。**因为 f(0,j) 有可能是有意义的，需要被转移更新**。 最终答案为 f(n,m).

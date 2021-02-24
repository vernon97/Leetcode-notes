<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-02-01 22:20:33
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-02-24 20:02:18
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week29.md
-->

# Week 29 - Leetcode 281 - 290

#### 282 - 给表达式添加运算符

给定一个仅包含数字 0-9 的字符串和一个目标值，在数字之间添加二元运算符（不是一元）+、- 或 * ，返回所有能够得到目标值的表达式。

这题显然就是DFS了 搜索每两个数字间可能出现的运算符并组合即可

问题就在于本题的方案如何记录， 这里用到了和线段树类似的运算表达式来简化 我们用一个形如

> `a + b * _` 的式子来表示所有方案 这样

```diff
+ 如果后面是 + : (a + b * c) + 1  * _
+ 如果后面是 - : (a + b * c) + -1 * _
+ 如果后面是 * : a + (b * c) * _
```

这里我们额外规定最后一个数后面可以跟 `+`

```cpp
using LL = long long;
class Solution {
public:
    vector<string> ans;
    string path;
    LL target;
public:
    vector<string> addOperators(string num, int target) {
        path.resize(100);
        this->target = target;
        dfs(num, 0, 0, 0, 1); // 最开始 0 + 1 * _
        return ans;
    }
    void dfs(string num, int u, int len, LL a, LL b)
    {
        if(u == num.size())
        {
            if(a == target) ans.push_back(path.substr(0, len - 1)); // 除去最后一个加号
        }
        else
        {
            LL c = 0;
            for(int i = u; i < num.size(); i++)
            {
                c = c * 10 + num[i] - '0';
                path[len++] = num[i];
                // +
                path[len] = '+';
                dfs(num, i + 1, len + 1, a + b * c, 1);

                if(i + 1 < num.size())
                {
                    // - 
                    path[len] = '-';
                    dfs(num, i + 1, len + 1, a + b * c, -1);
                    // *
                    path[len] = '*';
                    dfs(num, i + 1, len + 1, a, b * c);
                }
                // ! 注意这里前导0的情况
                if(num[u] == '0') break;
            }
        }  
    }
};
```

### 283 - 移动0

除了移动之外要保持非0元素的相对顺序 
所以与其是移动0 实际上是移动所有非0元素

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        // 除了移动之外要保持非0元素的相对顺序 
        // 所以与其是移动0 实际上是移动所有非0元素
        for(int i = 0, j = 0; i < nums.size(); i++)
        {
            if(nums[i] != 0)
            {
                swap(nums[i], nums[j]);
                j++;
            }
        }
    }
};
```

### 284 - 顶端迭代器

这题思路上是比较简单的 无非是增加一个缓存 和一个bool标记，调用peek 相当于提前调用了next, 再调用next的时候直接返回即可；

这里学习一下怎么调用父类同名函数 (指定namespace)

```cpp
class PeekingIterator : public Iterator {
public:
	int curNum;
	bool flag;
public:
	PeekingIterator(const vector<int>& nums) : Iterator(nums) {
	    // Initialize any member here.
	    // **DO NOT** save a copy of nums and manipulate it directly.
	    // You should only use the Iterator interface methods.
		flag = false;
	}
    // Returns the next element in the iteration without advancing the iterator.
	int peek() {
        if(flag == false)
		{
			curNum = Iterator::next();
			flag = true;
		}
		return curNum;
	}
	
	// hasNext() and next() should behave the same as in the Iterator interface.
	// Override them if needed.
	int next() {
		if(flag == true)
		{
			flag = false;
			return curNum;
		}
		return Iterator::next();
	}
	
	bool hasNext() const {
	    return flag || Iterator::hasNext();
	}
};
```

### 287 - 寻找重复数

这题经典套路了，看到1 ~ n 组成的数组就可以往数组元素作为下标想

这题就是把数组元素作为下标 连边 找到图中的环的起点，即为重复元素

图中环的起点怎么找呢？ 快慢指针：1. 找到环 2. 让一个指针领先一个环的长度 3. 同时前进直到相遇 此时为起点

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        // 已经是经典套路了 以数组值做下标 转化为找环的入口的问题
        // 1. 找到环
        int slow = 0, fast = 0;
        do
        {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }while(slow != fast);
        int cur = 0;
        // 2. 让cur指针提前走一个环的长度
        do
        {
            cur = nums[cur];
            slow = nums[slow];
        }while(slow != fast);
        // 3. 从头 直到相遇
        int head = 0;
        while(head != cur)
        {
            head = nums[head];
            cur = nums[cur];
        }
        return cur;
    }
};
```

### 289 - 生命游戏

> 你可以使用原地算法解决本题吗？
> 请注意，面板上所有格子需要同时被更新：
> 你不能先更新某些格子，然后使用它们的更新后的值再更新其他格子。

很显然就是要额外定义状态 

上一个状态 + 这一个状态
1 - 0 -> 2
1 - 1 -> 3

计算附近格子数的时候按照原来的状态 而不是更新过的状态

```cpp
class Solution {
public:
    int n, m;
public:
    void gameOfLife(vector<vector<int>>& board) {
        // 1 - 0 记作 2   0 - 1 记作 3
        n = board.size();
        m = board[0].size();
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
            {
                int cnt = getActivateCellsCount(board, i, j);
                if(board[i][j] == 1)
                {
                    if(cnt < 2 || cnt > 3) board[i][j] = 2;
                }
                else
                {
                    if(cnt == 3) board[i][j] = 3;
                }
            }
        // 把 2 和 3 都改写为 0 1
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
            {
                if(board[i][j] == 2) board[i][j] = 0;
                if(board[i][j] == 3) board[i][j] = 1;
            }
        
    }
    int getActivateCellsCount(vector<vector<int>>& board, int si, int sj)
    {
        int cnt = 0;
        for(int i = si - 1; i <= si + 1; i++)
        {
            if(i < 0 || i >= n) continue;
            for(int j = sj - 1; j <= sj + 1; j++)
            {
                if(j < 0 || j >= m) continue;
                if(i == si && j == sj) continue;
                if(board[i][j] == 1 || board[i][j] == 2) cnt++;
            }
        }
        return cnt;

    }
};
```

### 290 - 单词规律

还是比较基础的 之前应该是有一道一样的题（忘了是哪个了）

记录单词间的映射 注意这里不能只记录`pattern` 到 `s` 的映射 还要记录s中的单词是否曾经出现过（避免一个pattern对应多个单词）

最后通过判断i是否遍历到最后一个 来保证是一一对应的映射

```cpp
class Solution {
public:
    bool wordPattern(string pattern, string s) {
        stringstream ss(s);
        string str;
        unordered_map<char, string> hash;
        unordered_set<string> sset;
        int i = 0;
        while(ss >> str)
        {
            if(i >= pattern.size()) return false;
            char c = pattern[i++];
            if(!hash.count(c))
            {
                if(sset.count(str)) return false;
                hash[c] = str;
                sset.insert(str);
            }
            else
            {
                if(hash[c] != str) return false;
            }
        }
        return i == pattern.size();
    }
};
```

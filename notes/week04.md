<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-11-22 21:28:13
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-11-24 20:13:37
 * @FilePath: /Leetcode-notes/week04.md
-->
# Week 04 - Leetcode 31 - 40

#### 31 - 下一个排列

```diff
+ 这题真的是写一次忘一次
```

实际上本题是模拟一个C++中的一个库函数 next_permutation, 用法和sort差不多；
定义在头文件algorithm中，有next_permutation 和 prev_permutation两种；
如果存在a之后的排列，就返回true。如果a是最后一个排列没有后继，返回false，每执行一次，a就变成它的后继;

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        next_permutation(nums.begin(), nums.end());
    }
};
```

当然这么写肯定会被打了（逃

实际上，从后往前，找到第一个非降序的位置，找到第一个比当前元素大的元素并交换。

找下一个排列就是从后往前寻找第一个出现降的地方，把这个地方的数字与后边某个比它大的的数字交换，再把该位置之后整理为升序。

否则从数组末尾往前找，找到 第一个 位置`j`，使得 `nums[j] < nums[j + 1]`。

如果不存在这样的 `j`，则说明数组是不递增的，直接将数组逆转即可。

如果存在这样的 `j`，则从末尾找到第一个位置 `i > j`，使得 `nums[i] > nums[j]`。

交换 `nums[i]` 与 `nums[j]`，然后将数组从 `j + 1` 到末尾部分逆转

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int k = nums.size() - 1;
        // 1. 找到第一个递减点 e,g, 1,2,3 -> 1,3,2
        while(k > 0 && nums[k - 1] >= nums[k]) k--;
        if(k == 0) reverse(nums.begin(), nums.end()); // 如果降序排列 直接反转
        else
        {
            // 2. 找到 k - 1 右边第一个比nums[k - 1]大的元素
            int i = nums.size() - 1;
            while(i > k && nums[i] <= nums[k - 1]) i--;
            swap(nums[k - 1], nums[i]);
            // 交换完之后仍然是降序（字典序最大) 转换为升序(字典序最小)
            reverse(nums.begin() + k, nums.end());
        }
    }
};
```

其中，找右边第一个比`nums[k - 1]`大的元素可以用什么优化 -> __二分__

__二分优化版__

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int k = nums.size() - 1;
        // 1. 找到第一个递减点 e,g, 1,2,3 -> 1,3,2
        while(k > 0 && nums[k - 1] >= nums[k]) k--;
        if(k == 0) reverse(nums.begin(), nums.end()); // 如果降序排列 直接反转
        else
        {
            // 2. 找到 k - 1 右边第一个比nums[k - 1]大的元素
            //int i = nums.size() - 1;
            //while(i > k && nums[i] <= nums[k - 1]) i--;
            int l = k, r = nums.size() - 1;
            while(l < r)
            {
                int mid = l + r + 1 >> 1;
                if(nums[mid] > nums[k - 1]) l = mid;
                else r = mid - 1;
            }
            swap(nums[k - 1], nums[r]);
            // 交换完之后仍然是降序（字典序最大) 转换为升序(字典序最小)
            reverse(nums.begin() + k, nums.end());
        }
    }
};
```

#### 32 - 最长有效括号

```diff
- 经典劝退题
```

有这样一个假设
> 标记任何不满足`s[1..i]`中左括号数量>=右括号数量的位置，所有有效括号序列都不会经过这些位置

证明：反证法↓
![avatar](../figs/03.jpeg)

__所以整个序列被分成了不同的段，每一段都一定满足左括号的数量>=右括号的数量__

在每一段内 用栈来对应左右括号 记录最大长度

如果栈为空，则证明这是一个不合法位置，更新start（即为开始下一段）

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        stack<int> stk;

        int res = 0;
        for(int i = 0, start = -1; i < s.size(); i++)
        {
            if(s[i] == '(') stk.push(i);
            else
            {
                if(stk.size())
                {
                    stk.pop();
                    if(stk.size())
                        res = max(res,  i - stk.top());
                    else
                        res = max(res, i - start);
                }
                else
                {
                    start = i;
                }
            }
        }
        return res;
    }
};
```

#### 33 - 搜索旋转排序数组

二分一把梭
![avatar](../figs/04.jpeg)

对于nums仅有一个元素的情况而言，后面有可能出现`l = 1`但是`r = 0`的情况的;
所以最后的提交要写 nums[r]而不是nums[l];

最后下面刚好用了两种不同的二分模板，边界问题记住 __有加就有减__ 就好了

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        if(nums.empty()) return -1;
        // 1. 二分查找分界线
        int l = 0, r = nums.size() - 1;
        while(l < r)
        {
            int mid = l + r + 1 >> 1;
            if (nums[mid] >= nums[0])   l = mid;
            else r = mid - 1;
        }
        // 左边的一段 or 右边的一段
        if(target >= nums[0]) l = 0; // r = r;
        else l++, r = nums.size() - 1;
        // 正常的二分查找
        while(l < r)
        {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1; 
        }
        return nums[r] == target ? l : -1;
    }
};
```

#### 34 - 在排序数组中查找元素的第一个和最后一个位置

二分一把梭

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res = {-1, -1};
        if(nums.empty()) return res; 
        // 二分左边界
        int l = 0, r = nums.size() - 1;
        while(l < r)
        {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }

        if(nums[l] != target) return res;
        // 二分右边界
        else res[0] = l;
        r = nums.size() - 1;
        while(l < r)
        {
            int mid = l + r + 1 >> 1;
            if(nums[mid] <= target) l = mid;
            else r = mid - 1;
        }
        res[1] = l;
        return res;
    }
};
```

#### 35 - 搜索插入位置

基础二分，唯一注意一下二分到右端点的时候， 如果`nums[l] < target`的话，l++才是最终插入的位置；

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if(nums.empty()) return 0;
        int l = 0, r = nums.size() - 1;
        while(l < r)
        {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if(nums[l] < target) l++;
        return l;
    }
};
```

#### 36 - 有效的数独

模拟题 记录行 列 九宫格中是否有元素出现过就好了

```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        // 模拟题 判断行列九宫格是否有重复元素
        bool st_row[9][9], st_col[9][9], st_block[9][9];
        memset(st_row, false, sizeof st_row);
        memset(st_col, false, sizeof st_col);
        memset(st_block, false, sizeof st_block);
        for(int i = 0; i < 9; i++)
            for(int j = 0; j < 9; j++)
                if(board[i][j] != '.')
                {
                    int val = board[i][j] - '1';
                    if(st_row[i][val] || st_col[j][val] || st_block[i / 3 + (j / 3) * 3][val]) 
                        return false;
                    st_row[i][val] = st_col[j][val] = st_block[i / 3 + (j / 3) * 3][val] = true;
                }
        return true;
    }
};
```

#### 37 - 解数独

```diff
+ dfs + 状态压缩
```

还是很经典的DFS 回溯 + 状态压缩 + 剪枝策略；
复习一下DFS的剪枝策略：

```diff
+ 优化搜索顺序： 大部分情况下应该优先搜索分支较少的节点
+ 排除等效冗余： 如果不考虑顺序，要按照组合数搜索；
+ 可行性剪枝： 不可行提前返回
+ 最优性剪枝：如果不能当前最优更好的话可以直接返回；
```

状态压缩用一个九位的二进制表示可以填数的位置，1表示该为可行 0 表示不行；
可以通过位运算直接得到同时符合`行`， `列`， `九宫格`要求的候选方案， 然后搜索就可以了；
搜索的时候在所有剩余没填的地方中找到扩展方案数最小的地方搜索`（剪枝策略：优化搜索顺序）`

`ones[M]`记录 一个二进制数中有几个一，`power_2` 记录log运算；

```cpp
class Solution {
public:
    static const int N = 9, M = 1 << 9;
    int row[N], col[N], cell[3][3];
    int ones[M];
    unordered_map<int, int> power_2; 
public:
    // 用一个九位的二进制数表示可以填数的位置 1表示该位可行 0 表示不行； 
    int get(int x, int y)
    {
        return row[x] & col[y] & cell[x / 3][y / 3];
    }

    void draw(int x, int y, int t, bool is_set, vector<vector<char>>& board)
    {
        if(is_set) board[x][y] = '1' + t;
        else board[x][y] = '.';
        int v = 1 << t;
        if(!is_set) v = -v;
        row[x] -= v, col[y] -= v, cell[x / 3][y / 3] -= v;
    }
    bool dfs(int cnt, vector<vector<char>>& board)
    {
        if(!cnt) return true;
        int x, y, minv = 10;
        for(int i = 0; i < N; i++)
            for(int j = 0; j < N; j++)
            {
                if(board[i][j] == '.')
                {
                    int state = get(i, j);
                    if(ones[state] < minv)
                    {
                        x = i, y = j;
                        minv = ones[state];
                    }
                }
            }
        int state = get(x, y);
        for(int i = state; i ; i = i & (i - 1))
        {
            // lowbit 运算
            int t = power_2[i & (-i)];
            draw(x, y, t, true, board);
            if(dfs(cnt - 1, board)) return true;
            draw(x, y, t, false, board);
        }
        return false;
    }
    void solveSudoku(vector<vector<char>>& board) {
        // 初始化
        for(int i = 0; i < N; i++)
        {
            power_2[1 << i] = i;
            row[i] = col[i] = M - 1;
        }
        for(int i = 0; i < M; i++)
            for(int j = 0; j < N; j++)
                ones[i] += i >> j & 1;
        for(int i = 0; i < 3; i++)
            for(int j = 0; j < 3; j++)
                cell[i][j] = M - 1;
        // 写入状态
        int cnt = 0;
        for(int i = 0; i < N; i++)
            for(int j = 0; j < N; j++)
            {
                char cur_num = board[i][j];
                if(cur_num == '.') cnt++;
                else draw(i, j, cur_num - '1', true, board);
            }
        dfs(cnt, board);
    }
};
```

#### 38 - 外观数列

就嗯模拟, 记得用stringstream;

```cpp
class Solution {
public:
    string countAndSay(int n) {
        // 模拟吧 or 找规律
        string element = "1";
        if(n == 1) return element;
        for(int i = 1; i < n; i++)
        {
            stringstream ss;
            int si = 0;
            while(si < element.size())
            {
                char num = element[si];
                int cnt = 0;
                while(element[si] == num)
                    si++, cnt++;
                ss << cnt << num;
            }
            element = ss.str();
        }
        return element;
    }
};
```

#### 39 - 组合总和

DFS就好了

```cpp
class Solution {
public:
    vector<vector<int>> res;
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        // 优化搜索顺序 从大到小排序
        sort(candidates.begin(), candidates.end(), greater<int>());
        vector<int> path;
        dfs(0, target, path, candidates);
        return res;
    }
    void dfs(int si,int cur_sum, vector<int>& path, vector<int>& candidates)
    {
        if(cur_sum == 0)
        {
            res.push_back(path);
            return;
        }
        if(cur_sum < 0 || si >= candidates.size()) return;
        path.push_back(candidates[si]);
        dfs(si, cur_sum - candidates[si], path, candidates);
        path.pop_back();
        dfs(si + 1, cur_sum, path, candidates);
    }
};
```

#### 40 - 组合总和II

和上一题差不多 多了一个条件：__candidates 中的每个数字在每个组合中只能使用一次。__
上一题：

`不用 -> si 不变 // 用 -> si + 1`

这一题: 额外记录重复元素出现多少次
用的话还是`si + 1` 但是不用这个元素的话 就要过掉所有同样的元素避免重复

```cpp
class Solution {
public:
    vector<vector<int>> res;
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        // 优化搜索顺序 从大到小排序
        sort(candidates.begin(), candidates.end(), greater<int>());
        vector<int> path;
        dfs(0, target, path, candidates);
        return res;
    }
    void dfs(int si,int cur_sum, vector<int>& path, vector<int>& candidates)
    {
        if(cur_sum == 0)
        {
            res.push_back(path);
            return;
        }
        if(cur_sum < 0 || si >= candidates.size()) return;
        // 重复元素直接过掉
        int val = candidates[si], t = si;
        while(t < candidates.size() && candidates[t] == val) t++;
        path.push_back(candidates[si]);
        dfs(si + 1, cur_sum - val, path, candidates);
        path.pop_back();
        dfs(t, cur_sum, path, candidates);
    }
};
```

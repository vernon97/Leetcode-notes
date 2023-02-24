# Week 42 - Leetcode 411 - 420

### 412 - FizzBuzz

这题好像没什么好说的 模拟题

```cpp
class Solution {
public:
    vector<string> fizzBuzz(int n) {
        vector<string> res;
        for(int i = 1; i <= n; i++) {
            if(i % 3 == 0 && i % 5 == 0) res.push_back("FizzBuzz");
            else if(i % 3 == 0) res.push_back("Fizz");
            else if(i % 5 == 0) res.push_back("Buzz");
            else res.push_back(to_string(i));
        }
        return res;
    }
};
```

### 413 - 等差数列划分

比较基础的一维DP，f[i]指以i这个元素为结尾的等差数列方案数，最终答案就是求和；

可以优化下存储，因为状态转移只依赖前一个item
```cpp
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int n = nums.size();
        if (n < 3) return 0;
        int res = 0, f = 0;
        for(int i = 2; i < n; i++){
            if(2 * nums[i - 1] == nums[i] + nums[i - 2]) f ++;
            else f = 0;
            res += f;
        }
        return res;
    }
};
```
### 414 - 第三大的数

o(n)的话 就维护好前三个数的轮换关系就可以了

```cpp
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        //时间复杂度 o(n)
        int n = nums.size();
        long long res1 = LLONG_MIN, res2 = LLONG_MIN, res3 = LLONG_MIN;
        for(int x : nums) {
            if(x == res1 || x == res2 || x == res3) continue;
            if(x > res1) res3 = res2, res2 = res1, res1 = x;
            else if (x > res2) res3 = res2, res2 = x;
            else if (x > res3) res3 = x;
        }
        if(res3 != LLONG_MIN) return res3;
        return res1;
    }
};
```

### 415 - 字符串相加

高精度加法，实际就是列竖式加法啦 记得进位

```cpp
class Solution {
public:
    string addStrings(string num1, string num2) {
        // 高精度加法哈
        if(num1.size() < num2.size()) return addStrings(num2, num1);
        reverse(num1.begin(), num1.end());
        reverse(num2.begin(), num2.end());
        int t = 0;
        vector<int> res;
        for(int i = 0; i < num1.size(); i++) {
            t += num1[i] - '0';
            if(i < num2.size()) t += num2[i] - '0';
            res.push_back(t % 10);
            t /= 10;
        }
        if(t) res.push_back(t);
        stringstream ss;
        for(int i = res.size() - 1; i >= 0; i--) ss << res[i];
        return ss.str();
    }
};
```

### 417 - 太平洋大西洋水流问题

经典的floodfill

```cpp
class Solution {
public:
    int dx[4] = {1, 0, -1, 0}, dy[4] = {0, 1, 0, -1};
    int n, m;
    unordered_map<int, bool> hash_a, hash_b;
    vector<vector<int>> heights;
public:
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& _heights) {
        heights = _heights;
        n = heights.size(), m = heights[0].size();
        vector<vector<int>> res;
        for(int i = 0; i < n; i++) dfs(i, 0, hash_a), dfs(i, m - 1, hash_b);
        for(int i = 0; i < m; i++) dfs(0, i, hash_a), dfs(n - 1, i, hash_b);
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
                if(hash_a[210 * i + j] && hash_b[210 * i + j]) res.push_back({i, j});
        return res;
    }
    void dfs(int i, int j, unordered_map<int, bool>& hash) {
        if(hash.count(210 * i + j)) return;
        hash[210 * i + j] = true;
        for(int x = 0; x < 4; x++) {
            int nx = i + dx[x], ny = j + dy[x];
            if(nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
            if(heights[i][j] > heights[nx][ny]) continue;
            dfs(nx, ny, hash);
        }
    }
};
```

### 419 - 甲板上的战舰

有点像脑筋急转弯哈，本题水平/竖直，且互不挨着；

这样的话 每个独立的战舰可以用他左上角的值唯一代表，只要满足上边左边都是空即可

这样只需扫描一次 且额外空间是o(1)

```cpp
class Solution {
public:
    int countBattleships(vector<vector<char>>& board) {
        int res = 0;
        int n = board.size(), m = board[0].size();
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
            {
                if(board[i][j] == 'X' 
                    && (i == 0 || board[i - 1][j] == '.')
                    && (j == 0 || board[i][j - 1] == '.'))
                res++;
            }
        return res;
    }
};
```
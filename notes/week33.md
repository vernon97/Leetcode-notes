<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-05-05 21:07:20
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-05-05 21:54:01
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/week33.md
-->
# Week 33 - Leetcode 321 - 330

### 321 - 拼接最大数

首先把这个问题拆分成如下的子问题：

- 枚举从第一个数组中选几个数
- 从一个长度为m的数组中，按顺序选择k个数，使其字典序最大
- 按顺序合并两个数组，使其字典序最大

总结一下，按顺序选择子序列使其字典序最大这样都是贪心+栈 来删除，这里和316题是一个思路 都是比较大小，能删就删

**学习一下这个代码**

```cpp
vector<int> maxArray(vector<int>& nums, int k)
{
    int n = nums.size();
    vector<int> res(k);
    for(int i = 0, j = 0; i < nums.size(); i++)
    {
        int x = nums[i];
        // 这里的逻辑是 如果栈里有元素 且栈顶元素比x小，且弹出后还能凑成k个数的话（能删） 就删
        while(j && res[j - 1] < x && j + n - i > k) j--;
        if(j < k) res[j++] = x; // 如果可以加入就加入 这里学习一下
    }
}
```

按顺序合并两个子数组使得字典序最大，也是同样的贪心思路，按大的元素合并；

**如果两个元素相同的话** 则比较后面的元素 选择后面元素大的那一排 

代码中利用到了vector的比较大小 这里直接是按照字典序比较的 所以简洁很多 这里学习一下

```cpp
class Solution {
public:
    vector<int> maxNumber(vector<int>& nums1, vector<int>& nums2, int k) {
        int n = nums1.size(), m = nums2.size();
        vector<int> res(k, 0);
        for(int i = max(0, k - m); i <= min(k, n); i++)
        {
            auto a = maxArray(nums1, i);
            auto b = maxArray(nums2, k - i);
            auto c = mergeArray(a, b);
            res = max(res, c);
        }
        return res;
    }
    vector<int> maxArray(vector<int>& nums, int k)
    {
        int n = nums.size();
        vector<int> res(k);
        for(int i = 0, j = 0; i < nums.size(); i++)
        {
            int x = nums[i];
            while(j && res[j - 1] < x && j + n - i > k) j--;
            if(j < k) res[j++] = x;
        }
        return res;
    }
    vector<int> mergeArray(vector<int>& a, vector<int>& b)
    {
        vector<int> res;
        while(a.size() && b.size())
            if(a > b) // 注意啊注意 这里是vector的比较-> 字典序
                res.push_back(a[0]), a.erase(a.begin());
            else 
                res.push_back(b[0]), b.erase(b.begin());
        if(a.size()) res.insert(res.end(), a.begin(), a.end());
        if(b.size()) res.insert(res.end(), b.begin(), b.end());
        return res;
    }
};
```
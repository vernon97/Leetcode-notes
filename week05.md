<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2020-11-24 20:13:43
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2020-11-24 21:27:01
 * @FilePath: /Leetcode-notes/week05.md
-->
# Week 05 - Leetcode 41 - 50

#### 41 - 缺失的第一个正数
```diff
+ 原地Hash
```

这种要求常数复杂度的很多都是用数组元素当成数组下标完成一些操作，本题就是这样；

- 保证1出现在`nums[0]`的位置上，2出现在`nums[1]`的位置上，… ，`n`出现在`nums[n-1]`的位置上，其他的数字不管。例如`[3,4,-1,1]`将被排序为`[1,-1,3,4]`
  
- 遍历nums 找到第一个不满足`nums[i] == i + 1`的数即为答案；
  
要注意的点在于 是while 不是 if 换过来之后当前位置出现的新数字也需要判断；

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        //o(n) + 常数级别的额外空间
        int n = nums.size();
        for(int i = 0; i< n; i++)
        {
            while(nums[i] > 0 && nums[i] <= n && nums[i] != nums[nums[i] - 1]) // 在范围内 且 交换的两个元素不同 （避免死循环） -> 把nums[i] 放到下标为 nums[i] - 1的位置上
                swap(nums[i], nums[nums[i] - 1]);
        }

        for(int i = 0; i < n; i++)
            if(nums[i] != i + 1)
                return i + 1;
        return n + 1;
    }
};
```

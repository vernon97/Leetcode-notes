# Week 41 - Leetcode 301 - 410

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
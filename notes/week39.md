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
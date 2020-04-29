# LeetCode 1608 数组中数字出现的次数题解

## 题目描述

一个整型数组 nums 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

## 分析

我的第一思路是使用哈希(map)，可以达到$O(n)$的空间复杂度与$O(n\log{n})$的时间复杂度。

官方题解使用了位运算中的异或运算的性质，相同数字按位异或后的结果为0，不同数字按位异或之后的结果非0。

**按位异或运算的原理，相同位如果同时为1或者0,那么异或的结果是1,否则是0。**

异或运算的特性可以用于在一堆成对出现的数字中找到一个出现奇数次数的数字。



## 代码

### 1.我的代码

```c++
vector<int> singleNumbers(vector<int>& nums) {
    vector<int> ret;
    map<int, int> mp;
    for (int i = 0; i < nums.size(); i++) mp[nums[i]] = 0;
    for (int i = 0; i < nums.size(); i++) mp[nums[i]]++;
    for (auto iter = mp.begin(); iter != mp.end(); iter++) if (iter->second == 1) ret.push_back(iter->first);
    return ret;
}
```



### 2.官方题解

这道题目是找到两个只出现一次的数字，采用了分组异或的技术，这是因为两个不同的数字进行异或之后其结果中一定有一位为1,那么就按照这一位为0,1的情况来对元素进行分组，就可以找到元素。


https://leetcode.cn/problems/majority-element

### Think
```TXT
代码思路

排序数组：
使用 sort(nums.begin(), nums.end()) 对数组进行升序排序。
排序后，相同的元素会相邻排列。

返回中间元素：
直接返回排序后数组的中间元素 nums[nums.size()/2]。
由于多数元素的数量超过 n/2，排序后中间位置一定是多数元素。
```

### Ways
By C++
```C++
class Solution {
public:
  int majorityElement(vector<int> &nums) {
    sort(nums.begin(), nums.end());
    return nums[nums.size()/2];
  }  
};
```

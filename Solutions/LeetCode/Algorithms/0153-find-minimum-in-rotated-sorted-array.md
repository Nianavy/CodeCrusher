# Question

[find-minimum-in-rotated-sorted-array](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)



# Answer

> solution

通用二分逻辑：

维护 left 和 right 边界。
每次比较 nums[mid] 和 nums[right]。
若 nums[mid] > nums[right]，则最小值在右半部分（left = mid + 1）。
否则，最小值在左半部分或 mid 位置（right = mid）。
循环结束时，左右指针重合，即为最小值。

### Way Of C

> source code

```c
int findMin(const int* nums, int numsSize) {
    int left = 0;
    int right = numsSize - 1;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] > nums[right]) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return nums[left];
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;
        
        // 经典的二分查找，寻找分界点（旋转点）。
        // nums[mid] > nums[right]  -> 分界点在 mid 的右侧。
        // nums[mid] < nums[right]  -> 分界点在 mid 的左侧（或 mid 就是候选）。
        // 注意：没有考虑 nums[mid] == nums[right] 的情况，因为题目保证无重复元素。
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;  // 最小值在右半部分，排除 mid。
            } else {
                right = mid;     // mid 是可能的候选，包含它。
            }
        }
        return nums[left];  // 循环结束时 left == right，即为最小值。
    }
};
```

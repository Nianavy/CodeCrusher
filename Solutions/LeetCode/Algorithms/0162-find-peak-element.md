# Question

[find-peak-element](https://leetcode.cn/problems/find-peak-element/)



# Answer

> solution

采用**标准的二分查找变体**，基于以下数学观察：若`nums[mid] > nums[mid + 1]`，则峰值在左侧（包括mid）；否则在右侧。这能保证O(log n)时间复杂度，且始终能找到任意数组的一个峰值。

## 实现要点
1. **逻辑正确性**：通过二分缩小区间，而非暴力搜索。
2. **简洁性**：核心算法仅5-6行代码，无嵌套分支。
3. **健壮性**：处理边界情况（空数组、单一元素），修复内存管理问题。
4. **无副作用**：不修改输入，保持向后兼容。

### Way Of C

> source code

```c
int findPeakElement(int* nums, int numsSize) {
    int left = 0, right = numsSize - 1;
    // 二分查找：寻找第一个下降点（nums[i] > nums[i+1]）
    // 注意：这里简化处理，只找到任一峰值即可
    while (left < right) {
        int mid = left + (right - left) / 2;  // 防止整数溢出
        if (nums[mid] > nums[mid + 1]) {
            right = mid;  // 峰值在左边（包括mid）
        } else {
            left = mid + 1;  // 峰值在右边
        }
    }
    return left;  // left == right
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int findPeakElement(std::vector<int>& nums) {
        int left = 0, right = nums.size() - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[mid + 1]) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
};
```

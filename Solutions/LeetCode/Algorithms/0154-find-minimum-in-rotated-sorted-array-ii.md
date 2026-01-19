# Question

[find-minimum-in-rotated-sorted-array-ii](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)



# Answer

> solution

# **旋转数组最小值问题 - 解决思路总结**

## **核心思路**

**将旋转数组视为一个在中间某个点被“折断”的有序数组**，目标是找到这个“断点”所在的位置（即最小值的下标）。

## **为什么找最小值？**

因为最小值就是旋转断裂的点。在它是有序数组时，所有数字都大于等于第一个数；在它被旋转后，最小值就成了数组第一个“逆序”的位置。

## **二分查找的关键区分**

二分查找的核心在于**比较中点值 `nums[mid]` 和右边界值 `nums[hi]`**，来判断最小值位于哪一半：

1.  **`nums[mid] < nums[hi]`**：说明最小值在左半段（包括 `mid` 本身）
2.  **`nums[mid] > nums[hi]`**：说明最小值在右半段（不包括 `mid` 本身）
3.  **`nums[mid] == nums[hi]`**：无法判断方向，只能缩小右边界（`hi--`）

## **为什么这样比较？**

因为右边界 `hi` 始终在整个旋转数组的右侧，而最小值可能在数组的左侧部分。
- 如果 `mid` 已经小于 `hi`，说明 `mid` 在最小值的右侧，最小值在左半段
- 如果 `mid` 大于 `hi`，说明 `mid` 在最小值的左侧，最小值在右半段

## **时间复杂度**

- **无重复值时**：O(log n) - 标准二分查找
- **有重复值时**：最坏情况 O(n) - 因为遇到 `nums[mid] == nums[hi]` 时只能 `hi--` 逐个缩小

## **关键要点**

1.  **不用和首元素比较**：通过和右边界比较，避免了处理特殊的终止条件
2.  **循环条件是 `l < r`**：不断缩小区间，直到只剩一个元素（`l == r`）时，就是最小值的下标
3.  **重复值处理**：当 `nums[mid] == nums[hi]` 时，`hi--` 是合理且必要的，这是算法设计的代

### Way Of C

> source code

```c
int findMin(int* nums, int numsSize)
{
    int lo = 0;
    int hi = numsSize - 1;
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        // 关键判断：中点值 < 右边界值 -> 最小值在左半部（包括mid）
        if (nums[mid] < nums[hi]) {
            hi = mid;
        }
        // 中点值 > 右边界值 -> 最小值在右半部（不包括mid）
        else if (nums[mid] > nums[hi]) {
            lo = mid + 1;
        }
        // 中点值 == 右边界值 -> 无法判断，缩小右边界
        else {
            hi--;
        }
    }
    return nums[lo];
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int findMin(std::vector<int>& nums) {
        int l = 0;
        int r = nums.size() - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (nums[mid] < nums[r]) {
                r = mid;
            } else if (nums[mid] > nums[r]) {
                l = mid + 1;
            } else {
                r--; // 与 C 版本一致，处理重复值无法判断方向的情况
            }
        }
        
        return nums[l];
    }
};
```

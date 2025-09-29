# Question

[search-insert-position](https://leetcode.cn/problems/search-insert-position/)



# Answer

> solution

解决这个问题的核心思路是：**在排序数组中，利用二分查找快速定位到“第一个大于或等于 `target` 的元素”**。这个位置，无论是 `target` 已经存在还是需要插入，都是 `target` 应该占据的正确索引。这正是 `std::lower_bound` 所做的。

### **在排序数组中查找目标值插入位置的解决思路总结**

`searchInsert` 问题的目标是：在一个已排序的数组中找到目标值 `target` 应该插入的位置，以保持数组的有序性。如果 `target` 已经存在于数组中，则返回其第一次出现的索引。

这个问题的本质是查找**“第一个大于或等于 `target` 的元素的索引”**。这正是二分查找的一个经典应用场景，通常被称为**查找下界 (Lower Bound)**。

**核心思路分解：**

1.  **确定搜索区间**：
    *   标准的二分查找通常在闭区间 `[low, high]` 内进行。
    *   为了更方便地处理边界情况（特别是 `target` 大于所有元素时，插入位置是 `nums.size()`），一种非常优雅的模板是使用**左闭右开区间 `[low, high)`**，其中 `high` 初始化为 `nums.size()`。
        *   `low` 从 `0` 开始。
        *   `high` 从 `nums.size()` 开始。
        *   循环条件为 `while (low < high)`。

2.  **二分查找过程**：
    *   **计算 `mid`**：`mid = low + (high - low) / 2`。这种计算方式可以防止 `low + high` 溢出，并确保 `mid` 偏向左侧。
    *   **比较 `nums[mid]` 与 `target`**：
        *   如果 `nums[mid] >= target`：
            *   这说明 `mid` 位置的元素**可能**是我们要找的第一个大于或等于 `target` 的元素，或者 `target` 在 `mid` 的左侧。
            *   因此，我们将**右边界 `high` 收缩到 `mid`**。我们保留 `mid` 作为潜在答案，并尝试在 `[low, mid)` 范围内继续寻找更小的索引。
        *   如果 `nums[mid] < target`：
            *   这说明 `mid` 位置的元素太小了，`target` 肯定在 `mid` 的右侧。
            *   因此，我们将**左边界 `low` 移动到 `mid + 1`**。

3.  **循环终止与结果**：
    *   当循环 `while (low < high)` 终止时，`low` 和 `high` 将会相等。
    *   此时，`low` (或 `high`) 指向的就是数组中**第一个大于或等于 `target` 的元素的索引**。
        *   如果 `target` 存在于数组中，`low` 就是其第一次出现的索引。
        *   如果 `target` 不存在：
            *   如果 `target` 小于所有元素，`low` 将为 `0`。
            *   如果 `target` 大于所有元素，`low` 将为 `nums.size()`。
            *   如果 `target` 在数组中间的某个位置，`low` 将指向比 `target` 大的第一个元素。
    *   所有这些情况，`low` 都正确地表示了 `target` 应该被插入的位置。

**核心思想总结：**

`searchInsert` 问题的解决思路就是将它转化为一个**查找下界（`lower_bound`）**的问题。通过精心设计的二分查找模板，我们可以在 O(logN) 的时间复杂度内，精确地找到 `target` 在排序数组中的正确插入位置，而无需特殊处理 `target` 存在与否、或者在数组两端等边界情况。这种模板的简洁和鲁棒性，使其成为解决这类问题的首选。

### Way Of C

> source code

```c
int searchInsert(int* nums, int numsSize, int target) {
    // 使用 low = -1, high = numsSize
    int low = -1;
    int high = numsSize;
    while (low + 1 < high) {
        int mid = low + (high - low) / 2;
        if (target > nums[mid]) {
            low = mid;
        } else {
            high = mid;
        }
    }
    return high; // 返回 hi 作为插入位置
}
```

---

### Way Of C++

> source code

```c++

class Solution {
public:
    int searchInsert(std::vector<int>& nums, int target) {
        // 使用 [low, high) 搜索区间 (左闭右开)
        // low 初始化为 0，high 初始化为 nums.size()
        // 这样即使 target 比所有元素都大，high 最终也会是 nums.size()，表示插入到末尾
        // 如果 target 比所有元素都小，high 最终会是 0，表示插入到开头
        int low = 0;
        int high = nums.size();

        while (low < high) { // 循环条件：当搜索区间至少有一个元素时
            int mid = low + (high - low) / 2; // 安全计算 mid，偏向左侧

            if (nums[mid] >= target) {
                // 如果 mid 元素大于等于 target，说明 target 应该在 mid 或 mid 的左侧
                // mid 可能就是我们要找的插入位置，所以将 high 收缩到 mid
                high = mid;
            } else {
                // 如果 mid 元素小于 target，说明 target 肯定在 mid 的右侧
                // 将 low 移动到 mid + 1
                low = mid + 1;
            }
        }
        // 循环结束后，low (和 high) 将指向第一个大于或等于 target 的元素的位置。
        // 这正是 target 应该被插入的位置。
        return low;
    }
};
```

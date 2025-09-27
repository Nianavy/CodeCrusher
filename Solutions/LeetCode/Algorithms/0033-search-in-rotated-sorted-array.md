# Question

[search-in-rotated-sorted-array](https://leetcode.cn/problems/search-in-rotated-sorted-array/)



# Answer

> solution

解决这个问题的核心思路是：**利用旋转数组的“分段有序”特性，每次二分查找都能排除一半元素。** 关键在于，在每次迭代中，你必须能确定 `mid` 所在的那个子数组（左半部分或右半部分）是**有序**的，然后根据 `target` 的值和有序部分的边界来判断 `target` 是否在该有序部分中。如果不在，`target` 必然在另一半，即便那一部分是旋转的，也已经缩小了范围。

### **在旋转排序数组中查找目标值的解决思路总结**

在旋转排序数组中查找目标值的挑战在于，数组整体不再完全有序，传统的二分查找不能直接应用。然而，旋转数组有一个关键特性：它总是可以被分为**两个有序的子数组**。基于此，有两种主要的解决思路：

#### **思路一：单次二分查找 (推荐，更简洁)**

这种方法在一个二分查找循环中同时解决“找哪一段有序”和“目标是否在该段”的问题。

1.  **初始化**：设置 `low = 0` 和 `high = numsSize - 1`。
2.  **循环条件**：`while (low <= high)`。
3.  **计算 `mid`**：`mid = low + (high - low) / 2`。
4.  **直接命中**：如果 `nums[mid] == target`，直接返回 `mid`。
5.  **判断 `mid` 哪一边是有序的**：
    *   **情况 A: `[low, mid]` 是有序的** (即 `nums[low] <= nums[mid]`)。
        *   检查 `target` 是否落在这个有序区间内：`nums[low] <= target && target < nums[mid]`。
            *   如果**是**：说明 `target` 在左半段，将 `high = mid - 1`。
            *   如果**不是**：说明 `target` 在右半段（旋转部分），将 `low = mid + 1`。
    *   **情况 B: `[mid, high]` 是有序的** (即 `nums[low] > nums[mid]`，因为 `[low, mid]` 乱序，所以右边必然有序)。
        *   检查 `target` 是否落在这个有序区间内：`nums[mid] < target && target <= nums[high]`。
            *   如果**是**：说明 `target` 在右半段，将 `low = mid + 1`。
            *   如果**不是**：说明 `target` 在左半段（旋转部分），将 `high = mid - 1`。
6.  **循环结束**：如果循环结束仍未找到 `target`，返回 `-1`。

**为什么这个思路很简洁？**
因为它通过 `nums[low]` 与 `nums[mid]` 的比较，总是能确定**至少一半的区间是有序的**。一旦确定了有序区间，就能利用标准的二分查找逻辑判断 `target` 是否在该区间。如果 `target` 不在该有序区间，那么它必然在另外那段“无序”的区间中，通过调整 `low` 或 `high`，搜索范围也会正确缩小。

#### **思路二：两次二分查找 (两阶段法)**

这种方法将问题分解为两个独立的二分查找阶段。

1.  **第一阶段：寻找旋转点 (或最大值索引)**
    *   进行一次二分查找，目标是找到数组中**最小元素的索引**（即旋转点）。
    *   具体做法是：
        *   比较 `nums[mid]` 和 `nums[high]`。
        *   如果 `nums[mid] > nums[high]`，说明 `mid` 在左侧有序部分，旋转点在 `[mid+1, high]`，所以 `low = mid + 1`。
        *   如果 `nums[mid] <= nums[high]`，说明 `mid` 在右侧有序部分或就是旋转点，旋转点在 `[low, mid]`，所以 `high = mid`。
    *   循环结束后，`low` (或 `high`) 将指向旋转点的索引。

2.  **第二阶段：在确定的有序区间内查找**
    *   根据第一阶段找到的旋转点，将原始数组分为两个**完全有序**的子数组。
    *   例如，如果旋转点索引是 `pivot`：
        *   第一个有序区间是 `[0, pivot-1]`。
        *   第二个有序区间是 `[pivot, numsSize-1]`。
    *   根据 `target` 的值与 `nums[0]` 和 `nums[numsSize-1]` 的关系，判断 `target` 可能在哪一个有序区间内。
    *   然后在确定的那个有序区间内，执行**标准的二分查找**。

**为什么这个思路也有效？**
它把复杂问题分解为简单子问题。先确定了“哪儿是旋转点”，然后问题就退化成了在两个普通有序数组中查找。

#### **核心哲学联系：**

这两种思路都体现了“**实用主义**”和“**简洁执念**”。它们没有试图发明新的算法，而是巧妙地在现有二分查找的基础上进行调整。思路一更“简洁”，因为它在一个循环中处理了所有判断，减少了代码行数和函数调用。思路二更“清晰”，它将问题分解为独立的步骤，易于理解和调试。两者都是高效的 O(logN) 解决方案。

### Way Of C

> source code

```c
// 主函数：在旋转排序数组中查找目标值
// nums: 指向整数数组的指针
// numsSize: 数组中元素的数量
// target: 要查找的目标值
int search(int* nums, int numsSize, int target)
{
    // C 语言中，数组大小为 0 时 nums 可能为 NULL，直接返回 -1。
    // numsSize = 0 且 nums = NULL 是合法输入，但无法查找。
    if (numsSize == 0) {
        return -1;
    }

    int lo = 0;
    int hi = numsSize - 1;

    // 标准二分查找循环
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2; // 安全计算 mid，防止溢出

        if (nums[mid] == target) {
            return mid; // 找到目标，返回其索引
        }

        // 判断当前 mid 所在的半段是否是有序的
        // 情况一：[lo, mid] 这部分是有序的
        if (nums[lo] <= nums[mid]) {
            // 检查 target 是否落在 [lo, mid) 这个有序区间内
            if (nums[lo] <= target && target < nums[mid]) {
                hi = mid - 1; // target 在左侧有序区间，缩小右边界
            } else {
                lo = mid + 1; // target 不在左侧有序区间，转向右侧 [mid+1, hi] 查找
            }
        } 
        // 情况二：[lo, mid] 无序，那么 [mid, hi] 这部分一定是有序的
        else { 
            // 检查 target 是否落在 (mid, hi] 这个有序区间内
            if (nums[mid] < target && target <= nums[hi]) {
                lo = mid + 1; // target 在右侧有序区间，缩小左边界
            } else {
                hi = mid - 1; // target 不在右侧有序区间，转向左侧 [lo, mid-1] 查找
            }
        }
    }

    return -1; // 循环结束，未找到目标
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int search(std::vector<int>& nums, int target) {
        if (nums.empty()) return -1; // 空数组处理

        int n = nums.size();
        int l = 0, r = n - 1;

        // 阶段一：找到旋转点 (实际上是找到左侧有序子数组的最大值索引)
        // 这里的循环结束后，l (或 r) 将指向左侧有序部分的最后一个元素
        // 例如 [4,5,6,7,0,1,2] -> l=3 (nums[3]=7)
        // 例如 [1,2,3,4,5,6,7] -> l=6 (nums[6]=7)
        while (l < r) {
            int mid = l + r + 1 >> 1; // mid 偏右，确保 l 不会死循环 (l=mid, r=mid-1)
            if (nums[mid] >= nums[0]) { // mid 在左侧有序区间，或在旋转点之前
                l = mid;
            } else { // mid 在右侧有序区间，旋转点在 mid 或更左
                r = mid - 1;
            }
        }
        // 此时 l 是左侧有序子数组的最大值索引（或整个数组的最大值索引，如果未旋转）

        // 阶段二：根据 target 确定搜索区间
        // 判断 target 是在左侧有序子数组 (包含 nums[0]) 还是右侧有序子数组中
        if (target >= nums[0]) { // target 在 [0, l] 区间
            r = l; // l 已经是这个区间的右边界
            l = 0; // 重新设置左边界
        } else { // target 在 [l+1, n-1] 区间
            l = l + 1; // 重新设置左边界
            r = n - 1; // 重新设置右边界
        }

        // 阶段三：在确定的有序区间内进行标准二分查找
        // 这里的循环结束后，l == r，指向目标可能的唯一位置
        while (l < r) {
            int mid = l + r >> 1; // mid 偏左，确保 r 不会死循环 (l=mid+1, r=mid)
            if (nums[mid] >= target) {
                r = mid; // target 在 mid 或 mid 的左侧
            } else {
                l = mid + 1; // target 在 mid 的右侧
            }
        }

        // 检查最终 l (或 r) 位置的元素是否是目标值
        if (nums[r] == target) {
            return r;
        }
        return -1; // 未找到
    }
};
```

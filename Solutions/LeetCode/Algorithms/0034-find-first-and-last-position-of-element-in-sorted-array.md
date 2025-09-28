# Question

[find-first-and-last-position-of-element-in-sorted-array](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)



# Answer

> solution

解决这个问题的核心思路是：**将问题分解为两次独立的、但都基于二分查找的子任务**。一次是查找目标值的**最左侧（起始）出现位置**，另一次是查找目标值的**最右侧（结束）出现位置**。这两次查找都必须在找到 `target` 时**不立即停止**，而是继续向特定方向搜索，以确保找到真正的边界。

### **在排序数组中查找目标值的起始和结束位置的解决思路总结**

在排序数组中查找目标值的起始和结束位置 (`searchRange`) 的问题，不能通过一次简单的二分查找来解决，因为标准的二分查找只返回任意一个匹配的索引。我们需要两次修改过的二分查找。

**核心思路分解：**

1.  **第一次二分查找：寻找目标值的“最左侧出现位置” (First Occurrence)**
    *   **目标**：找到数组中**第一个等于 `target` 的元素**的索引。如果 `target` 存在多个，我们希望得到最小的那个索引。
    *   **二分查找变体**：
        *   初始化 `low = 0`, `high = nums.size() - 1`。
        *   在循环 `while (low <= high)` 中：
            *   计算 `mid = low + (high - low) / 2`。
            *   如果 `nums[mid] == target`：
                *   我们找到了一个 `target`，这可能就是最左侧的。将 `ans = mid` 并继续向左搜索，即 `high = mid - 1`，看是否有更小的索引。
            *   如果 `nums[mid] < target`：
                *   `target` 在 `mid` 的右侧，将 `low = mid + 1`。
            *   如果 `nums[mid] > target`：
                *   `target` 在 `mid` 的左侧，将 `high = mid - 1`。
        *   循环结束后，`ans` 中存储的就是第一个 `target` 的索引。如果没有找到 `target`，`ans` 仍然是其初始值（如 `-1`）。

2.  **第二次二分查找：寻找目标值的“最右侧出现位置” (Last Occurrence)**
    *   **目标**：找到数组中**最后一个等于 `target` 的元素**的索引。如果 `target` 存在多个，我们希望得到最大的那个索引。
    *   **二分查找变体**：
        *   重新初始化 `low = 0`, `high = nums.size() - 1` (或者，如果已知 `first_occurrence`，可以从 `first_occurrence` 开始搜索，`low = first_occurrence`)。
        *   在循环 `while (low <= high)` 中：
            *   计算 `mid = low + (high - low) / 2`。
            *   如果 `nums[mid] == target`：
                *   我们找到了一个 `target`，这可能就是最右侧的。将 `ans = mid` 并继续向右搜索，即 `low = mid + 1`，看是否有更大的索引。
            *   如果 `nums[mid] < target`：
                *   `target` 在 `mid` 的右侧，将 `low = mid + 1`。
            *   如果 `nums[mid] > target`：
                *   `target` 在 `mid` 的左侧，将 `high = mid - 1`。
        *   循环结束后，`ans` 中存储的就是最后一个 `target` 的索引。

**整体流程：**

1.  **处理空数组**：如果数组为空，直接返回 `{-1, -1}`。
2.  **执行第一次二分查找**：找到 `first_occurrence`。
3.  **检查 `first_occurrence`**：如果 `first_occurrence == -1` (或者 `nums[first_occurrence] != target`，取决于模板实现)，说明 `target` 不存在，直接返回 `{-1, -1}`。
4.  **执行第二次二分查找**：找到 `last_occurrence`。
5.  **返回结果**：返回 `{first_occurrence, last_occurrence}`。

**核心思想总结：**

这种方法体现了**“分而治之”**的原则。虽然是两次二分查找，但每次查找都是专门针对一个边界（左边界或右边界）。关键在于，当 `nums[mid] == target` 时，我们不是停止，而是将 `ans` 记录下来，然后**调整搜索区间向目标边界收敛**（找左边界时向左收敛，找右边界时向右收敛）。这样做确保了在 O(logN) 的时间复杂度内找到准确的边界。

### Way Of C

> source code

```c
static int binary_search_begin(int *a, int size, int target)
{
    int lo = 0; // 搜索区间的左边界
    int hi = size - 1; // 搜索区间的右边界
    int ans = -1; // 存储找到的第一个 target 索引

    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] >= target) {
            ans = mid; // 记录当前可能的答案
            hi = mid - 1; // 尝试在左半部分找更靠左的 target
        } else {
            lo = mid + 1; // target 在 mid 右侧
        }
    }
    
    // 此时 ans 可能是第一个 >= target 的索引。
    // 但需要额外检查 a[ans] 是否真的等于 target。
    if (ans != -1 && a[ans] == target) {
        return ans;
    }
    return -1; // target 不存在
}

// 辅助函数：二分查找最后一个 target 出现的位置
// 查找范围是 [0, size-1]，返回的是最后一个 <= target 的索引
static int binary_search_end(int *a, int size, int target)
{
    int lo = 0; // 搜索区间的左边界
    int hi = size - 1; // 搜索区间的右边界
    int ans = -1; // 存储找到的最后一个 target 索引

    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] <= target) {
            ans = mid; // 记录当前可能的答案
            lo = mid + 1; // 尝试在右半部分找更靠右的 target
        } else {
            hi = mid - 1; // target 在 mid 左侧
        }
    }

    // 此时 ans 可能是最后一个 <= target 的索引。
    // 但需要额外检查 a[ans] 是否真的等于 target。
    if (ans != -1 && a[ans] == target) {
        return ans;
    }
    return -1; // target 不存在
}


/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* searchRange(int* nums, int numsSize, int target, int* returnSize) {
    int *range = malloc(2 * sizeof(int));
    if (range == NULL) {
        fprintf(stderr, "Memory allocation failed for result range.\n");
        exit(EXIT_FAILURE);
    }
    *returnSize = 2;
    
    // 如果数组为空，直接返回 {-1, -1}
    if (numsSize == 0) {
        range[0] = -1;
        range[1] = -1;
        return range;
    }

    range[0] = binary_search_begin(nums, numsSize, target);
    // 只有当起始位置找到时，才去查找结束位置。
    // 这样可以避免在 target 不存在时，对 binary_search_end 进行不必要的调用。
    // 同时，如果 target 存在，我们知道它至少在 range[0] 位置，可以把 search_end 的搜索范围缩小到 [range[0], numsSize - 1]
    // 进一步优化：
    if (range[0] != -1) {
        // 如果 target 存在，从 first_occurrence 开始查找最后一个 target 会更高效
        range[1] = binary_search_end(nums + range[0], numsSize - range[0], target);
        // 但是 binary_search_end 返回的是相对于子数组的索引，需要加回基准
        if (range[1] != -1) {
             range[1] += range[0];
        } else {
            // 这个分支不应该被触发，因为如果 first_occurrence 找到了，end 也一定能找到
            // 但是为了安全起见，这里可以放一个默认值或者调试信息
            // 实际上，如果 binary_search_end 找不到，那说明 first_occurrence 也有问题
            range[1] = range[0]; // 如果只出现一次，结束位置就是开始位置
        }
    } else {
        range[1] = -1; // 如果起始位置都没找到，结束位置自然也是 -1
    }

    return range;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::vector<int> searchRange(std::vector<int>& nums, int target) {
        if (nums.empty()) {
            return {-1, -1};
        }

        // 第一次二分查找：寻找第一个大于等于 target 的位置 (即 target 的起始位置)
        // 这是一个查找下界 (lower_bound) 的二分查找模板
        int l = 0, r = nums.size() - 1;
        while (l < r) {
            // 正确的 mid 偏左计算方式 (避免 int 溢出，并确保 l=mid, r=mid-1 不死循环)
            int mid = l + (r - l) / 2; 
            if (nums[mid] >= target) {
                r = mid; // 如果 mid 满足条件，尝试在左半部分找更左的
            } else {
                l = mid + 1; // mid 太小，往右找
            }
        }
        // 循环结束后 l == r，指向第一个 >= target 的索引

        // 检查：如果 nums[l] 不是 target，说明 target 不存在
        if (nums[l] != target) {
            return {-1, -1};
        }

        int first_occurrence = l; // 记录找到的起始位置

        // 第二次二分查找：寻找第一个小于等于 target 的位置 (即 target 的结束位置)
        // 这是一个查找上界 (upper_bound) 的二分查找模板，但调整为找最后一个 <= target
        // 注意：这里的 l 从 first_occurrence 开始，可以缩小搜索范围，提高一点效率
        l = first_occurrence, r = nums.size() - 1; 
        while (l < r) {
            // 正确的 mid 偏右计算方式 (避免 int 溢出，并确保 l=mid+1, r=mid 不死循环)
            int mid = l + (r - l + 1) / 2; 
            if (nums[mid] <= target) {
                l = mid; // 如果 mid 满足条件，尝试在右半部分找更右的
            } else {
                r = mid - 1; // mid 太大，往左找
            }
        }
        // 循环结束后 l == r，指向最后一个 <= target 的索引

        return {first_occurrence, r}; // 返回起始和结束位置
    }
};
```

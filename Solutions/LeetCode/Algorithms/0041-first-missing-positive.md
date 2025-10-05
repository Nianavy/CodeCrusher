# Question

[first-missing-positive](https://leetcode.cn/problems/first-missing-positive/)



# Answer

> solution

 `firstMissingPositive` 实现的解决思路非常直接和清晰：

1.  **第一步永远是简化数据结构。** 在这个问题中，就是直接利用给定的 `int` 数组 `nums`，不引入任何额外的数据结构。这是一个巧妙的“原地哈希”技巧。
2.  **消除所有特殊情况，将它们融入通用逻辑。** 核心思想是将数字 `k` “归位”到数组的 `k-1` 索引位置。
    *   **忽略无关数字：** 负数、零以及大于 `numsSize` 的数字对找到“第一个缺失的正整数”毫无意义。它们可以保持原样，或者被其他有效数字替换。
    *   **处理重复数字：** 如果 `nums[i]` 已经等于 `nums[target_idx]`（即目标位置已经有了相同的值），则不需要交换，否则会陷入死循环。
    *   **空数组：** 作为最简单的特殊情况，直接返回 `1`。
3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **归位阶段：** 遍历数组。对于每个位置 `i` 的数字 `val`：
        *   首先判断 `val` 是否在有效范围 `[1, numsSize]` 内。
        *   然后判断 `val` 是否已经位于其“正确”的位置（即 `nums[val-1] == val`）。
        *   如果 `val` 有效且不在正确位置，就将其与 `nums[val-1]` 交换。**关键在于，如果交换后 `nums[i]` 变成了新的值，`i` 并不递增，而是继续处理当前 `i` 位置的新值，直到 `nums[i]` 变得无效或归位。** 这样确保每个位置都能被正确处理。
    *   **查找阶段：** 在归位完成后，再次遍历数组。第一个 `nums[i]` 不等于 `i+1` 的位置 `i`，就意味着 `i+1` 是第一个缺失的正整数。
    *   **所有都归位：** 如果遍历完整个数组后，所有 `nums[i]` 都等于 `i+1`，则说明 `[1, numsSize]` 中的所有正整数都存在，那么第一个缺失的正整数就是 `numsSize + 1`。
4.  **确保零破坏性，向后兼容是最高优先级。** 该算法通过原地修改数组实现，这是为了满足 O(1) 空间复杂度的要求。在 LeetCode 场景中这没有问题。在其他场景，如果需要保留原始数组，则必须复制一份，但这会改变空间复杂度。

这就是这些解决方案的全部精髓：**利用数组下标作为桶，通过原地交换将有效数字归位，最后通过一次扫描找到第一个不匹配的“桶”。** 简洁、高效、实用。

### Way Of C

> source code

```c
// 辅助函数，明确且直接
static inline void swap_int(int *a, int *b) // 避免与C++的std::swap冲突，加个后缀
{
    int tmp = *a;
    *a = *b;
    *b = tmp;
}

// 函数签名与你提供的一致
int firstMissingPositive(int* nums, int numsSize) {
    // 空数组特判
    if (numsSize == 0) {
        return 1;
    }

    // 归位阶段
    for (int i = 0; i < numsSize; ) {
        int val = nums[i];
        int target_idx = val - 1;

        // 条件判断顺序优化，更清晰
        // 1. val 必须是正数且在有效范围内 [1, numsSize]
        // 2. 当前位置的值 nums[i] 不等于目标位置的值 nums[target_idx]
        //    这个条件是避免在遇到重复值时陷入无限循环的关键。
        if (val > 0 && val <= numsSize && nums[i] != nums[target_idx]) {
            swap_int(&nums[i], &nums[target_idx]);
        } else {
            // 如果数字无效，或已经在正确位置，或目标位置已是它，则移动到下一个位置
            i++;
        }
    }

    // 查找阶段
    for (int i = 0; i < numsSize; i++) {
        if (nums[i] != i + 1) {
            return i + 1;
        }
    }

    // 如果所有数字都已归位，则缺失的是 numsSize + 1
    return numsSize + 1;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int firstMissingPositive(std::vector<int>& nums) {
        int n = nums.size(); // 缓存大小，避免重复调用

        // 空数组特判，这是最基本的情况
        if (n == 0) {
            return 1;
        }

        // 归位阶段：将数字 k 放到索引 k-1 的位置
        // 遍历整个数组，但内层 while 循环总共只会执行 O(N) 次 swap 操作
        for (int i = 0; i < n; ) {
            // 获取当前位置 i 的值，以及它“应该”被放置的目标索引
            long val = nums[i];
            long target_idx = val - 1;

            // 只有当 val 在 [1, n] 范围内，并且不在正确的位置，并且目标位置的值不是 val 时才进行交换
            // 注意条件顺序：先判断 val 是否有效，再判断是否需要交换
            if (val > 0 && val <= n && nums[i] != nums[target_idx]) {
                // 执行交换，将 val 放到其正确的位置
                std::swap(nums[i], nums[target_idx]);
            } else {
                // 如果 val 无效（<=0 或 >n），或者它已经在正确位置，或者目标位置已经是 val (重复值)
                // 那么当前位置 i 的数字已经“处理完毕”，移动到下一个位置
                i++;
            }
        }

        // 查找阶段：遍历归位后的数组，找到第一个不匹配的索引
        for (int i = 0; i < n; i++) {
            if (nums[i] != i + 1) {
                return i + 1; // 找到第一个不匹配的，就是缺失的最小正整数
            }
        }

        // 如果所有数字都已归位 (nums[i] == i + 1)，说明缺失的是 n + 1
        return n + 1;
    }
};
```

# Question

[3sum-closest](https://leetcode.cn/problems/3sum-closest/)



# Answer

> solution

1.  **预处理：排序。**
    *   **原因：** 这是双指针策略的基础。排序后，我们可以通过移动左右指针，高效地调整和的大小，而无需遍历整个数组。
    *   **操作：** 对输入数组 `nums` 进行升序排序。

2.  **固定一个元素，然后用双指针。**
    *   **外层循环：** 从头到尾遍历数组，依次将每个元素 `nums[i]` 作为三元组的第一个固定元素。
        *   **优化（可选但推荐）：** 跳过与前一个固定元素重复的 `nums[i]`，避免重复计算。
    *   **内层循环（双指针）：** 在 `nums[i]` 之后的子数组中，设置 `left = i + 1` 和 `right = numsSize - 1`。
        *   `left` 指针从 `i+1` 开始向右移动。
        *   `right` 指针从数组末尾向左移动。

3.  **计算当前和，并更新最接近值。**
    *   **计算：** 在双指针循环内部，计算 `current_sum = nums[i] + nums[left] + nums[right]`。
    *   **比较与更新：**
        *   比较 `|current_sum - target|` 与 `|closest_sum - target|`。
        *   如果当前和更接近目标，则更新 `closest_sum = current_sum`。
    *   **初始化 `closest_sum`：** 避免使用 `INT_MAX`。直接用数组前三个元素的和作为 `closest_sum` 的初始值，这样更直接，避免了边缘情况的判断。

4.  **根据当前和调整双指针。**
    *   **如果 `current_sum < target`：** 当前和太小了，需要增大。为了增大和，移动 `left` 指针 `left++`。
    *   **如果 `current_sum > target`：** 当前和太大了，需要减小。为了减小和，移动 `right` 指针 `right--`。
    *   **如果 `current_sum == target`：** 完美匹配！这已经是找到的最接近（甚至就是）目标的和，可以直接返回 `target`。

5.  **返回结果。**
    *   在外层循环结束后，`closest_sum` 将保存与目标值最接近的三元组之和。直接返回它。

**总结起来就是：排序，固定一个，双指针。在双指针内部，根据当前和与目标的关系，清晰地移动指针，并随时更新最佳答案。** 这才是解决这类问题最实用、最直接、效率最高的思路。

### Way Of C

> source code

```c
#include <stdlib.h> // for qsort, abs
#include <stdio.h>  // for NULL, if we needed it (not here)

// 自定义比较函数，用于 qsort
// qsort 需要一个 int (*compar)(const void *, const void *) 类型的函数
static int compare_ints(const void *a, const void *b) {
    return (*(const int *)a - *(const int *)b);
}

// Linus 的三数之和最接近目标值函数
// (在 C 语言中，我们通常不会用 class Solution 这种结构)
int threeSumClosest(int* nums, int numsSize, int target) {
    // 检查数组大小。如果小于 3，这问题没法解。
    // LeetCode 通常会保证 numsSize >= 3，但实际代码需要防御性编程。
    if (numsSize < 3) {
        // 根据 LeetCode 平台要求，可能需要返回一个特定值
        // 但为了通用性，这里直接返回 0 或错误码，或者干脆让调用者确保输入有效。
        // 对于这个特定问题，假定输入有效。
        return 0; // 这是一个不合理的返回值，仅为编译通过，实际应避免。
    }

    // 排序数组。qsort 是标准 C 库提供的。
    qsort(nums, numsSize, sizeof(int), compare_ints);

    // 初始化最接近的和。用数组前三个元素的和作为初始值。
    int closest_sum = nums[0] + nums[1] + nums[2];

    // 外层循环：固定第一个元素 nums[i]
    // i 至少要留出两个元素给 left 和 right，所以是 numsSize - 2
    for (int i = 0; i < numsSize - 2; ++i) {
        // 跳过重复的 i。
        // C 语言中，没有 std::vector 的成员函数，直接用索引。
        if (i > 0 && nums[i] == nums[i-1]) {
            continue;
        }

        int left = i + 1;             // 第二个元素从 i 的下一个开始
        int right = numsSize - 1;     // 第三个元素从数组末尾开始

        // 双指针遍历 i 之后的子数组
        while (left < right) {
            int current_sum = nums[i] + nums[left] + nums[right];

            // 检查当前和是否更接近目标值
            // C 语言中 abs() 用于 int
            if (abs(current_sum - target) < abs(closest_sum - target)) {
                closest_sum = current_sum;
            }

            // 根据当前和与目标的关系，移动指针
            if (current_sum < target) {
                left++; // 和太小，需要增大
            } else if (current_sum > target) {
                right--; // 和太大，需要减小
            } else {
                // 完美匹配，直接返回目标值
                return target;
            }
        }
    }

    return closest_sum;
}

/*
// 示例用法（可选，如果需要测试）
int main() {
    int nums1[] = {-1, 2, 1, -4};
    int numsSize1 = sizeof(nums1) / sizeof(nums1[0]);
    int target1 = 1;
    int result1 = threeSumClosest(nums1, numsSize1, target1);
    printf("Input: [-1, 2, 1, -4], Target: 1 -> Closest Sum: %d\n", result1); // Expected: 2

    int nums2[] = {0, 0, 0};
    int numsSize2 = sizeof(nums2) / sizeof(nums2[0]);
    int target2 = 1;
    int result2 = threeSumClosest(nums2, numsSize2, target2);
    printf("Input: [0, 0, 0], Target: 1 -> Closest Sum: %d\n", result2); // Expected: 0

    int nums3[] = {1, 1, -1, -1, 3};
    int numsSize3 = sizeof(nums3) / sizeof(nums3[0]);
    int target3 = -1;
    int result3 = threeSumClosest(nums3, numsSize3, target3);
    printf("Input: [1, 1, -1, -1, 3], Target: -1 -> Closest Sum: %d\n", result3); // Expected: -1

    return 0;
}
*/
```

---

### Way Of C++

> source code

```c++
#include <vector>
#include <algorithm> // for sort and abs
#include <climits>   // for INT_MAX (though we'll avoid it as much as possible)

class Solution {
public:
    int threeSumClosest(std::vector<int>& nums, int target) {
        // 先检查基本条件。如果数组少于3个元素，这问题就毫无意义。
        // 但根据 LeetCode 惯例，通常会保证至少有 3 个。
        // 为了健壮性，这里留空，但实际项目中应该处理。

        // 排序是必须的，这样我们才能用双指针。
        std::sort(nums.begin(), nums.end());

        // 初始化结果：用第一个可能的三元组和作为初始值，而不是 INT_MAX。
        // 这样可以避免在比较时处理极值，更直接。
        int closest_sum = nums[0] + nums[1] + nums[2]; 

        // 迭代 'i'，作为三元组的第一个元素。
        // 注意循环边界：'i' 至少要留出两个元素给 'left' 和 'right'。
        for (int i = 0; i < nums.size() - 2; ++i) {
            // 跳过重复的 'i'，避免重复计算相同的三元组。
            // 这是一个优化，不是严格必要，但能提高效率和避免冗余。
            if (i > 0 && nums[i] == nums[i-1]) {
                continue;
            }

            int left = i + 1;             // 第二个元素从 'i' 的下一个开始
            int right = nums.size() - 1;  // 第三个元素从数组末尾开始

            // 双指针遍历 'i' 之后的子数组
            while (left < right) {
                int current_sum = nums[i] + nums[left] + nums[right];

                // 检查当前和是否更接近目标
                if (std::abs(current_sum - target) < std::abs(closest_sum - target)) {
                    closest_sum = current_sum;
                }

                // 根据当前和与目标的关系，移动指针
                if (current_sum < target) {
                    left++; // 和太小，需要增加 'left' 指针来增大和
                } else if (current_sum > target) {
                    right--; // 和太大，需要减小 'right' 指针来减小和
                } else {
                    // 完美匹配，和正好等于目标。这就是我们要找的。
                    return target; 
                }
            }
        }

        return closest_sum;
    }
};
```

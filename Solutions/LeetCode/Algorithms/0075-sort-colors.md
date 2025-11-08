# Question

[sort-colors](https://leetcode.cn/problems/sort-colors/)



# Answer

> solution

颜色分类 (荷兰国旗算法)

### 1. 核心挑战

*   **限制**：数组中只有三种元素 (0, 1, 2)。
*   **目标**：在 $O(N)$ 时间复杂度下，**就地**完成排序，且**只进行一次遍历**（即最小化操作次数）。

### 2. 核心数据结构与状态定义

我们不使用任何额外的存储空间，而是使用三个移动的指针来维护数组中**三个分区**的状态：

| 指针名 | 作用 | 维护区域 | 状态 |
| :--- | :--- | :--- | :--- |
| `low` (或 `j`) | **0 区域的边界** | `[0, low-1]` | 都是 0 |
| `current` (或 `i`) | **当前扫描指针** | `[low, current-1]` | 都是 1 |
| `high` (或 `k`) | **2 区域的边界** | `[high+1, N-1]` | 都是 2 |

**未分类区域**：`[current, high]` 是我们尚未处理的区域。

### 3. 状态转移与指针管理 (单次遍历)

我们只需要遍历一次，从左向右移动 `current` 指针，根据 `nums[current]` 的值决定如何操作和移动边界。

| 当前元素 `nums[current]` | 动作 | 指针变动 | 哲学考量 |
| :--- | :--- | :--- | :--- |
| **0** | **交换到 `low` 区域**。将 `nums[current]` 与 `nums[low]` 交换。 | `low` 递增。`current` 递增。 | 换来的元素 (原 `nums[low]`) 保证是 1，可以安全地跳过。 |
| **2** | **交换到 `high` 区域**。将 `nums[current]` 与 `nums[high]` 交换。 | `high` 递减。`current` **不变**。 | 从 `high` 换来的元素可能是 0、1 或 2，必须在下一轮循环中重新检查，因此 `current` 不能前进。 |
| **1** | **保持原位**。它已经在 0 区域和 2 区域之间。 | `current` 递增。 | 元素 1 自动落在 $O(1)$ 区域。 |

### 4. 终止条件

当 `current` 超过 `high` 时，所有元素都已被处理。此时：

*   `[0, low-1]` 区域是 0。
*   `[low, high]` 区域是 1 (因为所有 0 和 2 都被移动了)。
*   `[high+1, N-1]` 区域是 2。

这个思路将复杂的排序问题分解为三个清晰、独立的指针操作，避免了不必要的二次扫描，是 $O(N)$ 复杂度的最佳实现。

### Way Of C

> source code

```c
static inline void swap_ptr(int *a, int *b)
{
    int tmp = *a;
    *a = *b;
    *b = tmp;
}

/**
 * 颜色分类：荷兰国旗算法 (O(N) 时间, O(1) 空间, 单次遍历)
 */
void sortColors(int* nums, int numsSize)
{
    if (numsSize <= 1) return;

    // low: 0s boundary (下一个 0 应该放的位置)
    // current: 当前扫描指针
    // high: 2s boundary (下一个 2 应该放的位置)
    int low = 0;
    int high = numsSize - 1;
    int current = 0; 

    // 只要当前扫描指针不超过 2 的边界
    while (current <= high) {
        if (nums[current] == 0) {
            // 发现 0：将其交换到 low 指针的位置
            swap_ptr(&nums[current], &nums[low]);
            low++;    // 扩展 0 的区域
            current++; // 当前位置已经是 1 或 0 (安全)，移动 current
        } else if (nums[current] == 2) {
            // 发现 2：将其交换到 high 指针的位置
            swap_ptr(&nums[current], &nums[high]);
            high--;   // 缩小未排序区域
            // 注意：不移动 current！因为从 high 换来的元素可能是 0, 1, 或 2，必须在下一轮循环中检查。
        } else { // nums[current] == 1
            // 发现 1：已经在 0 和 2 的区域之间，保持原位，移动 current
            current++;
        }
    }
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        if (nums.empty()) return;

        // j: 0s boundary (next position for a 0)
        // i: current scanning pointer
        // k: 2s boundary (next position for a 2)
        int j = 0; 
        int k = nums.size() - 1;
        int i = 0; 

        // 只要当前扫描指针不超过 2 的边界
        while (i <= k) {
            if (nums[i] == 0) {
                // Case 0: Swap current 0 with the element at j (the next 1/0 boundary)
                swap(nums[i], nums[j]);
                i++; // Current position is now 1, safe to move on
                j++; // Extend 0s boundary
            } else if (nums[i] == 2) {
                // Case 2: Swap current 2 with the element at k (the next 2 boundary)
                swap(nums[i], nums[k]);
                // WARNING: Do NOT increment i. The element swapped from k (k--) 
                // could be 0, 1, or 2, and must be re-checked.
                k--; // Shrink 2s boundary
            } else { // nums[i] == 1
                // Case 1: 1 is already in place (between 0s and 2s). Just move on.
                i++;
            }
        }
    }
};
```

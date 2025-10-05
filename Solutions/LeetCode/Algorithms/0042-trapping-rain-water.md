# Question

[trapping-rain-water](https://leetcode.cn/problems/trapping-rain-water/)



# Answer

> solution

解决“接雨水”问题，我们追求的是**简洁、高效、实用**。主要有两种实用且高效的思路：

1.  **双指针法 (O(N) 时间, O(1) 空间) - 我的首选：**
    *   **数据结构简化：** 摒弃一切不必要的额外空间，只用几个整型变量来跟踪左右指针和左右侧的最大高度。
    *   **核心逻辑：**
        1.  初始化两个指针 `left` 和 `right`，分别指向数组的开始和结束。
        2.  初始化 `left_max` 和 `right_max` 为 0，用于记录从左右两边向内遍历时遇到的最高柱子。
        3.  使用 `while (left < right)` 循环向内收敛。
        4.  在每次循环中，**先更新 `left_max` 和 `right_max`** 为当前 `height[left]` 和 `height[right]` 与各自历史最大值中的较大者。
        5.  **关键决策点：** 比较 `left_max` 和 `right_max`。
            *   **如果 `left_max < right_max`：** 这意味着当前 `left` 指针所在位置的蓄水高度，**只会被 `left_max` 限制**，因为右侧有一个比 `left_max` 更高的 `right_max` 在支撑。因此，计算 `left` 位置的蓄水量 (`left_max - height[left]`)，并 `left++`。
            *   **如果 `right_max <= left_max`：** 这意味着当前 `right` 指针所在位置的蓄水高度，**只会被 `right_max` 限制**。因此，计算 `right` 位置的蓄水量 (`right_max - height[right]`)，并 `right--`。
        6.  累加所有位置的蓄水量到 `res` 中。
    *   **优势：** 这是最优的解决方案，完美结合了时间和空间效率。

2.  **动态规划法 (O(N) 时间, O(N) 空间)：**
    *   **核心逻辑：**
        1.  创建两个辅助数组 `left_max_height` 和 `right_max_height`，大小与 `height` 数组相同。
        2.  **第一次遍历 (从左到右)：** 填充 `left_max_height` 数组，`left_max_height[i]` 存储 `height[0...i]` 中的最高柱子。
        3.  **第二次遍历 (从右到左)：** 填充 `right_max_height` 数组，`right_max_height[i]` 存储 `height[i...n-1]` 中的最高柱子。
        4.  **第三次遍历 (计算结果)：** 遍历 `height` 数组，对于每个位置 `i`，能接到的雨水是 `min(left_max_height[i], right_max_height[i]) - height[i]`。累加这些值。
    *   **优势：** 思路直观，易于理解和实现。
    *   **劣势：** 需要额外的 O(N) 空间，不如双指针法。

3.  **单调栈法 (O(N) 时间, O(N) 空间)：**
    *   **核心逻辑：**
        1.  维护一个单调递减的栈，存储柱子的索引。
        2.  遍历 `height` 数组。
        3.  如果当前柱子 `height[i]` 高于栈顶柱子：
            *   栈顶柱子出栈，它就是构成凹槽的底部。
            *   如果栈不为空，新的栈顶就是左边界，当前 `i` 是右边界。
            *   计算这个凹槽的蓄水量：`height = min(height[新栈顶], height[i]) - height[出栈柱子]`，`width = i - 新栈顶索引 - 1`。
            *   累加 `height * width`。
        4.  重复直到栈为空或栈顶柱子高于 `height[i]`。
        5.  将 `i` 入栈。
    *   **优势：** 适用于某些需要找到左右“更高”元素的变种问题。
    *   **劣势：** 逻辑相对复杂，容易出错，不如双指针法直观。

**总结：** 对于“接雨水”问题，**双指针法是兼顾性能和简洁性的最佳实践**。它直接地体现了蓄水量由左右最短板决定的核心思想，同时避免了额外的空间开销。

解决“接雨水”问题，最优雅、最符合“好品味”且实用主义的方法，就是**双指针法**。其思路如下：

1.  **第一步永远是简化数据结构。**
    *   **思想：** 避免使用额外的数组（如动态规划的 `lmax` 和 `rmax`）或复杂的数据结构（如单调栈），仅用几个指针和变量来解决问题。
    *   **实现：** 只需要 `left_ptr`、`right_ptr`、`left_max`、`right_max` 这四个整数变量。这是 O(1) 空间复杂度的关键。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   **边界条件：** 对于空数组或只包含一根柱子的数组，显然无法接水，直接返回 `0`。这是最简单的特殊情况处理。
    *   **通用性：** 算法的核心循环能够优雅地处理各种柱子高低起伏的情况，无需复杂的特殊逻辑。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **初始化：**
        *   `left_ptr` 指向数组最左端 (0)。
        *   `right_ptr` 指向数组最右端 (`heightSize - 1`)。
        *   `left_max` 初始化为 `0`，表示左侧目前为止的最高柱子。
        *   `right_max` 初始化为 `0`，表示右侧目前为止的最高柱子。
        *   `res` 初始化为 `0`，用于累加总蓄水量。
    *   **核心循环 (`while (left_ptr < right_ptr)`)：**
        *   **维护最高点：** 每移动一个指针，都更新它那边的 `left_max` 或 `right_max`。
            *   `left_max = max(height[left_ptr], left_max);`
            *   `right_max = max(height[right_ptr], right_max);`
        *   **决策与蓄水：** 这是最关键的一步。
            *   **判断哪边是“短板”：** 比较 `left_max` 和 `right_max`。
            *   **如果 `left_max < right_max`：** 这意味着左侧的当前最高点是限制蓄水量的短板。此时，`height[left_ptr]` 位置能接到的水高度就确定是 `left_max`（因为它肯定被 `right_max` 这条更长的板子包围住），蓄水量为 `left_max - height[left_ptr]`。计算后，`left_ptr` 向右移动一步。
            *   **如果 `right_max <= left_max`：** 这意味着右侧的当前最高点是限制蓄水量的短板（或者与左侧相等）。此时，`height[right_ptr]` 位置能接到的水高度就确定是 `right_max`，蓄水量为 `right_max - height[right_ptr]`。计算后，`right_ptr` 向左移动一步。
        *   **为什么这种逻辑有效？** 当我们确定了 `left_max` 或 `right_max` 中的较小者时，我们知道当前处理的这一侧的蓄水高度已被确定。例如，如果 `left_max < right_max`，那么 `height[left_ptr]` 处的蓄水高度就完全由 `left_max` 决定，因为它知道右边至少有一根和 `right_max` 一样高的柱子，足以支撑住 `left_max` 的水位线。我们无需担心右边将来是否会出现更高的柱子，因为 `right_max` 已经保证了右侧至少有这么高。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   该算法仅读取 `height` 数组，不进行任何修改，因此对调用者没有任何副作用。

**总结来说，双指针法的核心就是“木桶原理”的动态应用：通过两个指针从两端向中间移动，始终以当前两端最高点中较低的那个作为水位线，并从较低水位线的那一侧进行蓄水计算。这种方法既直观又高效。**



### Way Of C

> source code

```c
// 辅助函数：简洁直接
static inline void swap_int(int *a, int *b)
{
    int tmp = *a;
    *a = *b;
    *b = tmp;
}

// 函数签名与你提供的一致
int trap(int* height, int heightSize) {
    // 边界条件：空数组或单柱，无法接水
    if (heightSize <= 1) {
        return 0;
    }

    int res = 0;
    int left_ptr = 0;          // 左指针
    int right_ptr = heightSize - 1; // 右指针
    int left_max = 0;          // 左侧遇到的最高柱子
    int right_max = 0;         // 右侧遇到的最高柱子

    // 双指针向内收敛
    while (left_ptr < right_ptr) {
        // 更新左右两侧的最高柱子
        left_max = (height[left_ptr] > left_max) ? height[left_ptr] : left_max;
        right_max = (height[right_ptr] > right_max) ? height[right_ptr] : right_max;

        // 核心逻辑：哪边是短板，就从哪边计算蓄水量并移动指针
        if (left_max < right_max) { // 左侧短板
            res += left_max - height[left_ptr]; // 计算当前左指针位置的蓄水量
            left_ptr++; // 左指针向右移动
        } else { // 右侧短板 (包括相等的情况)
            res += right_max - height[right_ptr]; // 计算当前右指针位置的蓄水量
            right_ptr--; // 右指针向左移动
        }
    }

    return res;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int trap(std::vector<int>& height) {
        int n = height.size();

        // 边界条件：空数组或单柱，无法接水
        if (n <= 1) {
            return 0;
        }

        int res = 0;
        int left_ptr = 0;
        int right_ptr = n - 1;
        int left_max = 0;
        int right_max = 0;

        while (left_ptr < right_ptr) {
            // 更新左右两侧的最高柱子
            left_max = std::max(height[left_ptr], left_max);
            right_max = std::max(height[right_ptr], right_max);

            // 核心逻辑：哪边是短板，就从哪边计算蓄水量并移动指针
            if (left_max < right_max) {
                res += left_max - height[left_ptr];
                left_ptr++;
            } else {
                res += right_max - height[right_ptr];
                right_ptr--;
            }
        }

        return res;
    }
};
```

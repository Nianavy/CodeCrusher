# Question

[jump-game-ii](https://leetcode.cn/problems/jump-game-ii/)



# Answer

> solution

解决“跳跃游戏 II”问题的最佳实践是：**使用贪心算法，维护当前跳跃能到达的最远范围，以及下一步能跳到的最远范围，并在必要时更新跳跃次数。**

1.  **第一步永远是简化数据结构。**
    *   **思想：** 避免使用额外的数组（如动态规划的 `f` 数组），仅用几个整数变量来跟踪进度。
    *   **实现：** 只需要 `steps` (总跳跃次数)、`current_reach` (当前跳跃能到达的最远索引)、`next_farthest_reach` (下一步能跳到的最远索引)。这是 O(1) 空间复杂度的关键。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   **数组大小为 0 或 1：** 这是最简单的特殊情况。如果数组为空或只有一个元素，起点就是终点，无需跳跃，直接返回 `0`。这是算法的基准。
    *   **目标：** 始终是到达数组的最后一个索引 (`numsSize - 1`)。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **初始化：**
        *   `steps = 0`：初始跳跃次数。
        *   `current_reach = 0`：一开始在索引 0，当前能到达的最远索引也是 0。
        *   `next_farthest_reach = 0`：下一步能跳到的最远索引，初始也是 0。
    *   **核心循环 (`for (int i = 0; i < numsSize - 1; i++)`)：**
        *   **遍历范围：** 循环遍历到倒数第二个元素 (`numsSize - 2`) 即可。因为一旦我们能到达倒数第二个元素，就必然能从当前跳跃范围内的某个点跳到或越过最后一个元素，无需再增加跳跃次数。
        *   **更新 `next_farthest_reach`：** 在每一次循环中，`next_farthest_reach` 总是被更新为 `max(i + nums[i], next_farthest_reach)`。这表示从当前遍历到的位置 `i`，我们可以计算出它能跳到的最远位置，并与之前计算的 `next_farthest_reach` 比较，取最大值。`next_farthest_reach` 追踪的是**在当前跳跃范围内，我们下一步最远能跳到哪里**。
        *   **判断是否需要“起跳” (`if (i == current_reach)`)：** 这是贪心算法的核心。如果当前遍历的索引 `i` 已经达到了 `current_reach` 的边界：
            *   这意味着我们已经遍历完了上一次跳跃所能覆盖的所有点。
            *   现在必须进行一次跳跃，否则无法继续前进。所以，`steps++`。
            *   更新 `current_reach = next_farthest_reach`。这意味着下一次跳跃将从 `i+1` 到 `next_farthest_reach` 之间选择最佳点，新的 `current_reach` 是这次跳跃所能达到的最远范围。
            *   **为什么这是贪心？** 因为每当我们需要起跳时，我们总是选择跳到**在当前可达范围内能跳到的最远位置**（由 `next_farthest_reach` 决定），从而确保用最少的步数覆盖最长的距离。
    *   **最终结果：** 循环结束后，`steps` 就是从起点到终点的最小跳跃次数。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   该算法仅读取 `nums` 数组，不进行任何修改，因此对调用者没有任何副作用。

**总结来说，解决“跳跃游戏 II”的贪心思路是：一步一步地拓展可达范围。每当走到当前跳跃范围的边界时，就强制进行一次跳跃，并选择在当前范围内能跳到最远的点作为下一次跳跃的起点，从而最小化总跳跃次数。这是一种高效且直观的“跳到最远”策略。**

### Way Of C

> source code

```c
// 辅助函数：简洁直接
static inline int max(int a, int b)
{
    return a > b ? a : b;
}

int jump(int* nums, int numsSize) {
    // 边界条件：如果数组大小为 0 或 1，则无需跳跃，返回 0。
    // 对于 numsSize == 1，起点就是终点，跳 0 次。
    if (numsSize <= 1) {
        return 0;
    }

    int steps = 0;               // 记录总跳跃次数
    int current_reach = 0;       // 当前跳跃能够到达的最远索引
    int next_farthest_reach = 0; // 从当前跳跃范围内的任意一点能跳到的最远索引

    // 遍历到倒数第二个元素即可 (索引 n-2)
    // 因为一旦能到达倒数第二个元素，就必然能跳到或越过最后一个元素。
    // 最后一个元素 (numsSize-1) 不需要再计算它的跳跃能力。
    for (int i = 0; i < numsSize - 1; i++) {
        // 在当前跳跃范围 [i, current_reach] 内，
        // 尝试从 i 跳跃，更新下一步能到达的最远索引
        next_farthest_reach = max(i + nums[i], next_farthest_reach);

        // 如果当前遍历的索引 i 已经达到了 current_reach 的边界，
        // 说明我们必须进行一次跳跃了，否则无法前进。
        if (i == current_reach) {
            steps++; // 增加跳跃次数
            // 更新 current_reach 为通过这一次跳跃能到达的最远范围
            current_reach = next_farthest_reach;
            
            // 额外检查：如果更新后的 current_reach 仍然 <= i，
            // 且还没有到达终点 (i < numsSize - 1)，
            // 说明无法继续前进，问题无解。
            // 题目通常保证能到达，所以这个检查可以省略，但理论上是必要的。
            // if (current_reach <= i && i < numsSize - 1) {
            //     return -1; // 表示无法到达
            // }
        }
    }

    return steps;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int jump(std::vector<int>& nums) {
        int n = nums.size();

        // 边界条件：如果数组大小为 0 或 1，则无需跳跃，返回 0。
        // 对于 n=0，没法跳。对于 n=1，起点就是终点，跳 0 次。
        if (n <= 1) {
            return 0;
        }

        int steps = 0;               // 记录总跳跃次数
        int current_reach = 0;       // 当前跳跃能够到达的最远索引
        int next_farthest_reach = 0; // 从当前跳跃范围内的任意一点能跳到的最远索引

        // 遍历到倒数第二个元素 (索引 n-2)
        // 因为一旦能到达倒数第二个元素，就必然能跳到或越过最后一个元素。
        // 最后一个元素 (n-1) 不需要再计算它的跳跃能力。
        for (int i = 0; i < n - 1; i++) {
            // 总是更新 next_farthest_reach，它表示从 [0, i] 中任意一点
            // 能跳到的全局最远位置
            next_farthest_reach = std::max(i + nums[i], next_farthest_reach);

            // 如果当前遍历的索引 i 已经达到了 current_reach 的边界，
            // 说明我们必须进行一次跳跃了，否则无法前进到更远的区域。
            if (i == current_reach) {
                steps++; // 增加跳跃次数
                // 更新 current_reach 为通过这一次跳跃能到达的最远范围
                current_reach = next_farthest_reach;
                
                // 额外的防御性检查（通常题目保证能到达，故可省略）
                // 如果更新后发现 current_reach 仍然停滞不前 (即 <= i)，
                // 且尚未到达终点，说明无法前进。
                // if (current_reach <= i) { 
                //     return -1; // 表示无法到达
                // }
            }
        }

        return steps;
    }
};
```

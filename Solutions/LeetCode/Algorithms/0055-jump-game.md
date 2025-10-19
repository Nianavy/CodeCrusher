# Question

[jump-game](https://leetcode.cn/problems/jump-game/)



# Answer

> solution

### **解决思路总结：贪心算法 (最远可达距离)**

**格言：** “如果你能跳到最远，就跳到最远。短视的步骤只会带来麻烦，我们只需要关注终点是否在你能力范围之内。”

**核心思想：**
我们不需要模拟每一次可能的跳跃路径，因为目标只是判断**能否**到达终点。贪心算法的思想是：在每一步，我们都计算从当前位置出发，能够到达的最远索引位置 (`max_reach`)。只要当前正在检查的位置 `i` 在 `max_reach` 的范围内，我们就有能力继续前进。

1.  **定义贪心目标：**
    *   我们的目标是维护和最大化一个关键变量：`max_reach`，即从起始点（索引 0）到目前为止，我们能够跳到的最远索引位置。

2.  **初始化与迭代：**
    *   初始化 `max_reach = 0`。
    *   从数组的索引 `i = 0` 开始向后遍历。

3.  **核心判断与更新：**
    在遍历到位置 `i` 时，执行两个核心步骤：

    *   **可达性检查：** 检查当前位置 `i` 是否在我们已知的最远可达范围内。如果 `i > max_reach`，则说明我们无法到达位置 `i`，更不用说终点了，立即返回 `false`。
    *   **更新最远距离：** 如果 `i` 可达，我们计算从 `i` 跳跃能够到达的新位置 `i + nums[i]`，并用它来更新 `max_reach`。我们总是取当前 `max_reach` 和这个新位置的最大值。
        *   `max_reach = max(max_reach, i + nums[i])`

4.  **终止条件：**
    *   **成功终止：** 如果在任何时候，`max_reach` 达到或超过了数组的最后一个索引 (`nums.size() - 1`)，则我们找到了解决方案，可以立即返回 `true`，无需继续遍历。
    *   **失败终止：** 如果遍历到某个位置 `i`，发现 `i > max_reach`，则返回 `false`。

**简洁执念的体现：**
该方法通过一个单次遍历（O(N)）和仅一个变量 (`max_reach`)，将一个看似复杂的路径寻找问题，简化为一个简单的范围覆盖问题。这避免了递归或动态规划中存储中间状态的额外开销，是算法简洁性的典范。

### Way Of C

> source code

```c
static inline int c_max(int a, int b)
{
    return a > b ? a : b;
}

/**
 * 判断是否能跳到数组的最后一个索引。
 * 使用贪心算法：维护当前能够到达的最远索引（max_reach）。
 * 时间复杂度：O(N)
 * 空间复杂度：O(1)
 */
bool canJump(int* nums, int numsSize) {
    if (numsSize <= 1) {
        return true; // 长度为0或1的数组，默认可达
    }

    int max_reach = 0;
    
    // 遍历数组，注意：只需要遍历到倒数第二个元素 (numsSize - 1)，因为如果能到达那里，就一定能到达终点。
    // 但是为了简化逻辑，我们遍历到 max_reach 允许的范围，直到终点前。
    
    for (int i = 0; i < numsSize; i++) {
        // 检查：如果当前位置 i 已经超出了目前能到达的最远范围 max_reach，则不可达。
        if (i > max_reach) {
            return false;
        }
        
        // 更新 max_reach
        max_reach = c_max(max_reach, i + nums[i]);
        
        // 如果 max_reach 已经达到或超过了终点，则成功。立即返回，无需继续遍历。
        if (max_reach >= numsSize - 1) {
            return true;
        }
    }

    // 如果循环结束，且没有提前返回 false，那么必然 max_reach 已经 >= numsSize - 1。
    return max_reach >= numsSize - 1;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 判断是否能跳到数组的最后一个索引。
     * 采用贪心算法：维护当前能够到达的最远索引（max_reach）。
     * 时间复杂度：O(N)
     * 空间复杂度：O(1)
     */
    bool canJump(std::vector<int>& nums) {
        int max_reach = 0;
        int n = nums.size();

        // 遍历数组中的每一个位置 i
        for (int i = 0; i < n; ++i) {
            
            // 如果当前遍历的位置 i 已经超出了目前能到达的最远范围 max_reach，
            // 意味着位置 i 及其之后的所有位置都不可达。
            if (i > max_reach) {
                return false;
            }
            
            // 更新 max_reach：
            // 它是旧的 max_reach，或者是从当前位置 i 跳跃能够到达的新位置 (i + nums[i])，取两者最大值。
            max_reach = std::max(max_reach, i + nums[i]);

            // 优化：如果在循环中 max_reach 已经覆盖或超过了终点，
            // 就可以提前返回 true，无需继续遍历。
            if (max_reach >= n - 1) {
                return true;
            }
        }

        // 如果循环结束，max_reach 必然已经覆盖了终点 (n-1) 或者在循环内部已经返回了 true/false。
        // 对于正常结束的循环， max_reach >= n - 1 的检查保证了结果的正确性。
        // 但为了代码的健壮性，这里可以再检查一次（尽管在前面的 if 中已经处理了终点）。
        // 实际上，如果循环没有提前返回 false，那么必然是因为 max_reach 最终 >= n - 1。
        return max_reach >= n - 1; 
        
        // 可选 加上提前终止的优化：

        // for (int i = 0, j = 0; i < nums.size(); i ++ ) {
        //     if (j < i) return false;
        //     j = std::max(j, i + nums[i]);
        //     // 优化：如果 j 已经到达或超过终点，立即返回 true
        //     if (j >= nums.size() - 1) return true;
        // }
        // return true; // 只有当数组为空或单元素时，才可能在循环外返回 true

    }
};
```

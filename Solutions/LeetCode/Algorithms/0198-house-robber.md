# Question

[house-robber](https://leetcode.cn/problems/house-robber/)



# Answer

> solution

**【核心观点】**
好的代码，数据结构对了，复杂性就没了。

**【解题思路】**
1.  **数据结构**：只用两个整数状态（`taken` 和 `untaken`）表示“到当前房子为止是否偷了”的最大收益。无需记录所有历史状态，空间复杂度 [O(1)]。

2.  **状态转移**（无循环的顺序逻辑）：
    *   `taken = untaken (前一个未偷) + current_value`
    *   `untaken = max(taken (前一个已偷), untaken)`

3.  **边界处理**：空数组直接返回 0，循环机制自动处理，无需特殊 `if` 分支。

4.  **输出**：
    *   **C 版本**：直接操作数组指针，手动实现 `max`，代码长度最短，效率最高。
    *   **C++ 版本**：使用 `std::vector` 配合迭代器，调用 `std::max`，保持 STL 风格。

**【摘要】**
别为过去分配空间，只记住前一个。别为分支写多余逻辑，让过程决定结果。这就是“好品味”。

### Way Of C

> source code

```c
int rob(int* nums, int numsSize) {
    int taken = 0;
    int untaken = 0;
    for (int i = 0; i < numsSize; i++) {
        int last_taken = taken;
        /* 想办法偷当前房子：那就基于前一个“不偷”的状态 */
        taken = untaken + nums[i];
        /* 不偷当前房子：前一个状态中“偷”或“不偷”的最大值 */
        untaken = (last_taken > untaken) ? last_taken : untaken;
    }
    return (taken > untaken) ? taken : untaken;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int rob(std::vector<int>& nums) {
        int taken = 0;
        int untaken = 0;
        for (int num : nums) {
            int last_taken = taken;
            /* 偷当前房子 */
            taken = untaken + num;
            /* 不偷当前房子 */
            untaken = std::max(last_taken, untaken); // 这里使用 std::max 更清晰
        }
        return std::max(taken, untaken);
    }
};
```

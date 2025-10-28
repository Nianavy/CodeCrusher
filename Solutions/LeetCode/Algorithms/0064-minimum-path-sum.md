# Question

[minimum-path-sum](https://leetcode.cn/problems/minimum-path-sum/)



# Answer

> solution

### **解决思路总结：最小路径和的原地动态规划 (In-place DP)**

**格言：** “如果你有一个需要存储中间结果的 DP 表，而这个表恰好和你的输入长得一样，那就直接在输入上计算，不要浪费宝贵的内存。”

**核心思想：**
最小路径和的 DP 状态转移方程是 $DP[i][j] = \text{Grid}[i][j] + \min(DP[i-1][j], DP[i][j-1])$。
由于每个单元格的最小路径和，一旦计算出来，就不再需要其原始值，并且这个计算结果只会被其右侧和下侧的单元格使用，因此我们可以直接在输入数组 `grid` 上进行计算和存储，从而将空间复杂度降至 O(1) 额外空间。

1.  **处理边界：**
    在开始主 DP 循环之前，必须正确初始化第一行和第一列的累加路径和，因为它们只能从一个方向到达。
    *   **第一列 (向下)：** `Grid[i][0]` 只能从上方 `Grid[i-1][0]` 到达。因此，`Grid[i][0]` 存储的是从起点到 `(i, 0)` 的累加和。
    *   **第一行 (向右)：** `Grid[0][j]` 只能从左方 `Grid[0][j-1]` 到达。因此，`Grid[0][j]` 存储的是从起点到 `(0, j)` 的累加和。

2.  **状态转移（从 (1, 1) 开始）：**
    *   从 `(1, 1)` 位置开始，遍历网格的其余部分。
    *   对于每个单元格 `Grid[i][j]`，我们计算到达该点的最小路径和，并原地更新它：
        $$\text{Grid}[i][j] = \text{Grid}_{\text{original}}[i][j] + \min(\text{Grid}[i-1][j], \text{Grid}[i][j-1])$$
        *   这里的 `Grid[i-1][j]` 和 `Grid[i][j-1]` 都已经是预先计算好的最小路径和。

3.  **结果：**
    *   遍历完成后，网格右下角的元素 `Grid[N-1][M-1]` 就是从左上角到右下角的最小路径总和。

**实用主义的体现：**
原地 DP 是空间效率的终极体现。它在不增加任何额外空间分配的前提下，成功解决了问题，将 DP 框架与输入数据的内存结构完美结合，是最符合“简洁执念”和“实用主义”的解决方案。

### Way Of C

> source code

```c
static inline int c_min(int a, int b) {
    return a < b ? a : b;
}

/**
 * 最小路径和：原地动态规划 (In-place DP)
 * 时间复杂度：O(M*N)
 * 空间复杂度：O(1) 额外空间 (直接修改输入 grid)
 */
int minPathSum(int** grid, int gridSize, int* gridColSize) {
    int n = gridSize;      // 行数
    int m = gridColSize[0]; // 列数 (假设所有行相同)

    if (n == 0 || m == 0) {
        return 0;
    }

    // 1. 初始化第一列 (向下累加)
    for (int i = 1; i < n; ++i) {
        grid[i][0] += grid[i - 1][0];
    }

    // 2. 初始化第一行 (向右累加)
    for (int j = 1; j < m; ++j) {
        grid[0][j] += grid[0][j - 1];
    }

    // 3. 状态转移 (从 (1, 1) 开始)
    for (int i = 1; i < n; ++i) {
        for (int j = 1; j < m; ++j) {
            // grid[i][j] = 当前值 + min(上方路径和, 左侧路径和)
            grid[i][j] += c_min(grid[i - 1][j], grid[i][j - 1]);
        }
    }

    // 结果在右下角
    return grid[n - 1][m - 1];
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 最小路径和：原地动态规划 (In-place DP)
     * 时间复杂度：O(M*N)
     * 空间复杂度：O(1) 额外空间 (直接修改输入 grid)
     */
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) {
            return 0;
        }

        int n = grid.size();
        int m = grid[0].size();

        // 1. 初始化第一列 (向下累加)
        for (int i = 1; i < n; ++i) {
            grid[i][0] += grid[i - 1][0];
        }

        // 2. 初始化第一行 (向右累加)
        for (int j = 1; j < m; ++j) {
            grid[0][j] += grid[0][j - 1];
        }

        // 3. 状态转移 (从 (1, 1) 开始)
        for (int i = 1; i < n; ++i) {
            for (int j = 1; j < m; ++j) {
                // grid[i][j] = 当前值 + min(上方路径和, 左侧路径和)
                grid[i][j] += std::min(grid[i - 1][j], grid[i][j - 1]);
            }
        }

        // 结果在右下角
        return grid[n - 1][m - 1];
    }
};
```

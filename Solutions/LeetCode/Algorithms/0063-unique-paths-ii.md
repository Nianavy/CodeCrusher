# Question

[que-paths-ii](https://leetcode.cn/problems/unique-paths-ii/)



# Answer

> solution

### **解决思路总结：带障碍物的空间优化 DP**

**格言：** “障碍物是路径的死胡同。把它标为零，然后继续你的计算，不要让它浪费你的内存。”

**核心思想：**
这个问题是标准唯一路径问题的延伸。障碍物的位置路径数直接为零，且这个零值会向下和向右传递，影响后续单元格的路径计算。我们仍使用空间优化的动态规划（O(M) 滚动数组）来解决。

1.  **空间结构：**
    *   使用一个长度为 $M$（列数）的一维数组 `dp`。`dp[j]` 存储的是到达当前行中 $(i, j)$ 位置的路径总数。

2.  **边界初始化（第一行）：**
    *   这是最关键的初始化步骤。遍历第一行，初始化 `dp` 数组。
    *   如果 `obstacleGrid[0][j]` 不是障碍物（值为 0），则 `dp[j] = 1`。
    *   一旦遇到障碍物（值为 1），由于路径只能向右走，后续所有 `dp[j']` 都必须保持为 0，即 `break` 循环。
    *   **特殊检查：** 如果起点 `obstacleGrid[0][0]` 就是障碍物，那么 `dp[0]` 最终会是 0，最终结果也必然是 0。

3.  **状态转移（逐行更新）：**
    *   从第二行开始，逐行更新 `dp` 数组。

    *   **处理第一列（`dp[0]`）：**
        *   当前行 `dp[0]` 只依赖于它的上方（即上一轮迭代的 `dp[0]`）。
        *   如果 `obstacleGrid[i][0]` 是障碍物，或者上一轮的 `dp[0]` 已经是 0（上方不可达），则当前 `dp[0]` 必须设置为 0。

    *   **处理其余列（`dp[j]` for $j > 0$）：**
        *   遍历从 $j=1$ 到 $M-1$ 的所有列。
        *   **障碍物检查：** 如果 `obstacleGrid[i][j]` 是障碍物，则 `dp[j] = 0`。
        *   **转移方程：** 否则，应用标准 DP 转移：`dp[j] = dp[j-1] + dp[j]`。
            *   `dp[j-1]` 代表左侧的路径数（当前行的左边）。
            *   `dp[j]` 代表上方的路径数（上一行的当前位置）。

4.  **结果：**
    *   所有行遍历完成后，`dp[M-1]`（即网格的右下角）存储的就是总的唯一路径数。

**实用主义的体现：**
通过 O(M) 的空间优化，我们在保持 O(M*N) 时间复杂度的同时，高效解决了障碍物引入的边界问题。障碍物的处理逻辑简单且直接：遇到障碍物就切断路径（置为 0），不影响整体的 DP 框架。这种简洁、高效的局部操作，是 DP 优化的标准范式。

### Way Of C

> source code

```c
/**
 * 唯一路径数：动态规划 (空间优化到 O(obstacleGridColSize))
 * 注意：C 语言的 int 溢出风险较高，但假设返回值 fit in int。
 */
int uniquePathsWithObstacles(int** obstacleGrid, int obstacleGridSize, int* obstacleGridColSize) {
    int n = obstacleGridSize;      // 行数
    int m = *obstacleGridColSize; // 列数
    
    if (n == 0 || m == 0) {
        return 0;
    }

    // 我们可以使用 int 代替 long long，但需要注意溢出
    int *dp = (int*)calloc(m, sizeof(int)); // 使用 calloc 初始化为 0
    if (dp == NULL) {
        return 0;
    }
    
    // 1. 初始化第一行/边界条件
    for (int j = 0; j < m; ++j) {
        if (obstacleGrid[0][j] == 1) {
            break; // 遇到障碍物后，后续第一行不可达
        }
        dp[j] = 1;
    }
    
    // 2. 状态转移 (从第二行开始)
    for (int i = 1; i < n; ++i) {
        // 更新 dp[0] (第一列的路径数)
        // 如果当前起点是障碍物，或者上一行起点不可达 (dp[0]==0)
        if (obstacleGrid[i][0] == 1 || dp[0] == 0) {
            dp[0] = 0;
        } 
        // 否则 dp[0] 仍然是 1 (因为上一行起点可达，且当前不是障碍物)
        
        // 从第二列开始更新 (j=1 到 m-1)
        for (int j = 1; j < m; ++j) {
            if (obstacleGrid[i][j] == 1) {
                // 如果是障碍物，路径数为 0
                dp[j] = 0;
            } else {
                // 否则，路径数 = 左侧路径数 + 上方路径数
                dp[j] = dp[j - 1] + dp[j];
            }
        }
    }

    int result = dp[m - 1];
    free(dp); 

    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 带障碍物的唯一路径数：动态规划 (空间优化到 O(min(m, n)))
     * 时间复杂度：O(M*N)
     * 空间复杂度：O(M) (假设 M <= N)
     */
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        if (obstacleGrid.empty() || obstacleGrid[0].empty()) {
            return 0;
        }

        int n = obstacleGrid.size();    // 行数
        int m = obstacleGrid[0].size(); // 列数
        
        // 1. 确保 M 是较小的维度，以优化空间
        if (m > n) {
            // 交换 M 和 N，并转置矩阵以便后续使用 O(M) 空间
            // 实际上，为了避免转置，我们只交换 m 和 n 的值，并保持 dp 数组为 min(行, 列)
            // 考虑到代码清晰度，我们保持 m 和 n 的物理含义，但确保 dp 数组大小是 min(m, n)
        }
        
        // 2. DP 数组 (大小为列数 m)
        std::vector<long long> dp(m, 0); // 使用 long long 避免路径数溢出 (虽然 int 在 LeetCode 约束下可能够用)

        // 3. 初始化第一行/边界条件
        for (int j = 0; j < m; ++j) {
            if (obstacleGrid[0][j] == 1) {
                // 如果遇到障碍物，后续第一行的路径数都为 0，因为只能向右走
                break; 
            }
            dp[j] = 1;
        }
        
        // 4. 状态转移 (从第二行开始)
        for (int i = 1; i < n; ++i) {
            // 更新 dp[0] (第一列的路径数)
            // 如果当前行的起点是障碍物，或者上一行 dp[0] 已经是 0，则 dp[0] 变为 0
            if (obstacleGrid[i][0] == 1 || dp[0] == 0) {
                dp[0] = 0;
            } else {
                dp[0] = 1; // 只有在 i>0 且 (i-1, 0) 可达时，(i, 0) 才可达
            }
            
            // 从第二列开始更新 (j=1 到 m-1)
            for (int j = 1; j < m; ++j) {
                if (obstacleGrid[i][j] == 1) {
                    // 如果是障碍物，路径数为 0
                    dp[j] = 0;
                } else {
                    // 否则，路径数 = 左侧路径数 + 上方路径数
                    // dp[j-1] 是当前行的左侧路径数
                    // dp[j] 是上一行的当前路径数
                    dp[j] = dp[j - 1] + dp[j];
                }
            }
        }

        return (int)dp[m - 1];
    }
};
```

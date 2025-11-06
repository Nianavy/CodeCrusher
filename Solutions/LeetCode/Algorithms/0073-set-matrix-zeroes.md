# Question

[set-matrix-zeroes](https://leetcode.cn/problems/set-matrix-zeroes/)



# Answer

> solution

矩阵置零 ($O(1)$ 空间)

### 1. 核心挑战与策略

*   **挑战**：如何在不使用 $O(M \cdot N)$ 或 $O(M+N)$ 额外数组的情况下，记录哪些行和列需要被清零。
*   **策略**：将矩阵的第一行和第一列**征用**为状态标记空间。

### 2. 特殊情况：边界是数据也是标记

如果简单地将 $matrix[0][j]$ 和 $matrix[i][0]$ 用作标记，那么它们本身包含的原始数据（即第一行和第一列是否需要被清零）就会被覆盖。

*   **解决方案**：引入两个额外的 $O(1)$ 变量（例如 `first_row_zero` 和 `first_col_zero`）。
    *   `first_row_zero`：标记**整个第一行**是否需要被清零（基于原始数据）。
    *   `first_col_zero`：标记**整个第一列**是否需要被清零（基于原始数据）。
    *   矩阵内部的 $matrix[0][0]$ 单元格则可以只用于标记**内部**的行/列状态，它自己的状态由上述两个布尔变量接管。

### 3. 三阶段执行流程

我们必须精确地分步执行，以避免标记和清理操作相互干扰。

#### 阶段 I：标记 (扫描矩阵并记录状态)

*   **目标**：遍历 $matrix[0..R-1][0..C-1]$ 的所有元素。
*   **动作**：如果发现 $matrix[r][c] = 0$：
    1.  **更新标记**：设置 $matrix[0][c] = 0$（标记第 $c$ 列需要清零）。
    2.  **更新标记**：设置 $matrix[r][0] = 0$（标记第 $r$ 行需要清零）。
    3.  **特殊处理**：如果 $r=0$ 或 $c=0$，设置额外的布尔变量 (`first\_row\_zero` 或 `first\_col\_zero`)。

#### 阶段 II：内部置零 (应用标记)

*   **目标**：清理所有**非边界**的元素，即 $matrix[1..R-1][1..C-1]$。
*   **动作**：遍历内部矩阵，对于 $matrix[r][c]$：
    *   检查它的**标记**：如果 $matrix[r][0] = 0$（行被标记）或 $matrix[0][c] = 0$（列被标记），则 $matrix[r][c] = 0$。
*   **关键**：这一步必须在清理边界**之前**完成，以确保 $matrix[0][c]$ 和 $matrix[r][0]$ 中的标记信息在被清零前被用于内部元素。

#### 阶段 III：边界置零 (根据特殊标记)

*   **目标**：清理第一行和第一列。
*   **动作**：
    1.  如果 `first_row_zero` 为真，则将 $matrix[0][0..C-1]$ 全部置零。
    2.  如果 `first_col_zero` 为真，则将 $matrix[0..R-1][0]$ 全部置零。

这种分阶段、利用边界空间作为状态存储的策略，是解决 $O(1)$ 额外空间矩阵问题的**唯一实用方法**。

### Way Of C

> source code

```c
void setZeroes(int** matrix, int matrixSize, int* matrixColSize) 
{
    // matrixSize == rows (R)
    int rows = matrixSize;
    if (rows == 0 || matrixColSize == NULL || *matrixColSize == 0) return;

    // 获取列数 (C)。在矩形矩阵中，所有行的列数都相同。
    // 我们信任 matrixColSize[0] 提供了正确的列数。
    int cols = matrixColSize[0]; 

    bool first_row_zero = false;
    bool first_col_zero = false;

    // 阶段 1: 扫描并标记
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (matrix[r][c] == 0) {
                
                // 标记第一行和第一列的原始状态
                if (r == 0) first_row_zero = true;
                if (c == 0) first_col_zero = true;
                
                // 使用第一行/列作为标记空间
                matrix[0][c] = 0;
                matrix[r][0] = 0;
            }
        }
    }

    // 阶段 2: 内部置零 (跳过 r=0 和 c=0)
    for (int r = 1; r < rows; r++) {
        for (int c = 1; c < cols; c++) {
            // 如果该行的标记 (matrix[r][0]) 或该列的标记 (matrix[0][c]) 是 0
            if (matrix[r][0] == 0 || matrix[0][c] == 0) {
                matrix[r][c] = 0;
            }
        }
    }

    // 阶段 3: 边界置零 (使用之前存储的布尔状态)

    // 零化第一行
    if (first_row_zero) {
        // 使用 memset 必须准确知道字节大小
        memset(matrix[0], 0, cols * sizeof(int));
    }

    // 零化第一列
    if (first_col_zero) {
        for (int r = 0; r < rows; r++) {
            matrix[r][0] = 0;
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
    void setZeroes(vector<vector<int>>& matrix) {
        if (matrix.empty()) return;
        
        const int R = matrix.size();
        const int C = matrix[0].size();
        
        bool first_row_zero = false;
        bool first_col_zero = false;

        // 阶段 1: 扫描并标记
        for (int r = 0; r < R; ++r) {
            for (int c = 0; c < C; ++c) {
                if (matrix[r][c] == 0) {
                    // 特殊情况标记
                    if (r == 0) first_row_zero = true;
                    if (c == 0) first_col_zero = true;
                    
                    // 用第一行/列标记
                    matrix[0][c] = 0;
                    matrix[r][0] = 0;
                }
            }
        }

        // 阶段 2: 内部置零 (跳过第一行和第一列)
        for (int r = 1; r < R; ++r) {
            for (int c = 1; c < C; ++c) {
                // 如果该行或该列被标记为零
                if (matrix[r][0] == 0 || matrix[0][c] == 0) {
                    matrix[r][c] = 0;
                }
            }
        }

        // 阶段 3: 边界置零 (根据特殊标记)

        // 零化第一行 (如果需要)
        if (first_row_zero) {
            for (int c = 0; c < C; ++c) {
                matrix[0][c] = 0;
            }
        }

        // 零化第一列 (如果需要)
        if (first_col_zero) {
            for (int r = 0; r < R; ++r) {
                matrix[r][0] = 0;
            }
        }
    }
};
```

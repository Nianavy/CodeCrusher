# Question

[sudoku-solver](https://leetcode.cn/problems/sudoku-solver/)



# Answer

> solution

解决数独问题的核心思路是：**暴力尝试（回溯）并辅以高效剪枝**。我们从棋盘的第一个空格开始，尝试填充所有可能的合法数字（1-9）。每填充一个数字，就递归地尝试填充下一个空格。如果当前的选择导致无解，我们就“回溯”：撤销当前的选择，恢复棋盘和状态，尝试下一个数字。这个过程像一个探险家在迷宫中尝试路径，走不通就退回来换一条路。

### **数独求解 (solveSudoku) 的解决思路总结**

数独求解是一个典型的**约束满足问题**，非常适合使用**回溯算法（Backtracking）**来解决。回溯算法本质上是一种深度优先搜索（DFS），它系统地探索所有可能的解决方案，并在发现某个选择无法导致解决方案时，及时“剪枝”并撤销（回溯）。

**核心解决思路分解：**

1.  **状态管理与剪枝优化：**
    *   为了在 O(1) 时间内快速检查某个数字是否可以在给定单元格 `(r, c)` 填充，我们需要三个辅助的布尔数组（或哈希集）：
        *   `row_used[r][num]`：表示数字 `num` 是否已在第 `r` 行中出现。
        *   `col_used[c][num]`：表示数字 `num` 是否已在第 `c` 列中出现。
        *   `box_used[box_idx][num]`：表示数字 `num` 是否已在对应的 `3x3` 小方格 `box_idx` 中出现。
    *   这些数组在每次尝试填充数字时**更新**，在回溯时**恢复**，是算法高效的关键。

2.  **预处理阶段：**
    *   在开始 DFS 之前，遍历一次原始的数独棋盘。
    *   对于棋盘上**已经有数字的单元格**：
        *   将这些数字对应的 `row_used`, `col_used`, `box_used` 标记为 `true`。
    *   这一步是为了将棋盘的初始状态纳入到我们的剪枝逻辑中，避免重复计算。

3.  **深度优先搜索 (DFS) / 回溯函数 (`dfs_solve`)：**
    *   **参数**：通常需要棋盘 `board`，以及当前正在处理的单元格的位置。位置可以用 `(row, col)` 坐标对表示，也可以用一个线性索引 `cell_idx` (0 到 80) 来表示，`row = cell_idx / 9`, `col = cell_idx % 9`。
    *   **终止条件**：
        *   如果所有单元格都已处理完毕（例如 `cell_idx == 81` 或 `row == 9`），说明找到了一个有效的数独解。返回 `true`。
    *   **已填充单元格的处理**：
        *   如果当前单元格 `board[r][c]` 已经有数字（不是 `.`），直接跳过此单元格，递归调用 `dfs_solve` 处理下一个单元格。
    *   **空单元格的处理**：
        *   如果当前单元格是空的 (`.`)，尝试填充数字 `1` 到 `9`：
            *   对于每个数字 `num` (1-9)：
                *   **剪枝检查**：使用 `row_used[r][num]`, `col_used[c][num]`, `box_used[box_idx][num]` 快速检查 `num` 是否可以在 `(r, c)` 处合法填充。
                *   如果**合法**：
                    *   **尝试填充**：将 `num` 写入 `board[r][c]`。
                    *   **更新状态**：将 `row_used[r][num]`, `col_used[c][num]`, `box_used[box_idx][num]` 设为 `true`。
                    *   **递归**：调用 `dfs_solve` 处理下一个单元格。
                    *   如果递归返回 `true`：说明当前路径导致了一个解，则**当前 `dfs_solve` 也返回 `true`**（解已找到，无需再尝试）。
                *   如果**不合法**或递归返回 `false`（当前选择无法导致解）：
                    *   **回溯**：撤销当前的选择。将 `board[r][c]` 恢复为 `.`，并将 `row_used`, `col_used`, `box_used` 中对应的标记设回 `false`。然后尝试下一个数字 `num`。
    *   **所有尝试失败**：如果尝试了所有 `1` 到 `9` 的数字，都无法找到一个导致解的路径，则当前 `dfs_solve` 返回 `false`，通知上一层递归进行回溯。

**核心思想总结：**

数独求解的思路完美体现了**回溯算法**的精髓：**“先尝试，如果不行就退回，换个选择再尝试”**。通过预计算的 `row_used`, `col_used`, `box_used` 数组进行高效的**剪枝**，我们避免了大量的无效尝试，将指数级的搜索空间大大压缩。这种方法既系统又高效，是解决这类约束满足问题的通用范式。

### Way Of C

> source code

```c
// --- 全局静态标记数组 ---
// 对于 9x9 数独，这些标记数组是固定且小的。
// 使用全局静态数组避免了动态内存分配的开销和复杂性，
// 也避免了在递归函数中作为参数频繁传递。这是 C 语言中的常见优化。
static bool row_used[9][10]; // row_used[r][num] == true if num is used in row r
static bool col_used[9][10]; // col_used[c][num] == true if num is used in col c
static bool box_used[9][10]; // box_used[b_idx][num] == true if num is used in 3x3 box b_idx

// --- 深度优先搜索（回溯）函数 ---
// board: 数独棋盘的指针， char** 表示 char* 的数组
// cell_idx: 当前要处理的单元格的线性索引 (0-80)。
//           通过 cell_idx / 9 得到行索引，cell_idx % 9 得到列索引。
// 返回值：bool 类型，表示是否成功找到了一个解
static bool dfs_solve(char** board, int cell_idx) {
    // 终止条件：如果所有单元格 (0 到 80) 都已处理完毕
    if (cell_idx == 9 * 9) {
        return true; // 找到了一个完整的数独解
    }

    // 将线性索引 cell_idx 转换为 (row, col) 坐标
    int r = cell_idx / 9; // 行索引
    int c = cell_idx % 9; // 列索引
    
    // 计算当前单元格所属的 3x3 小方格的索引 (0-8)
    // box_idx = (r / 3) * 3 + (c / 3);
    // 这种计算方法是：
    // (r/3) 决定了 box 在行方向上的大块索引 (0, 1, 2)
    // (c/3) 决定了 box 在列方向上的大块索引 (0, 1, 2)
    // (r/3)*3 将行方向大块索引转换为 0, 3, 6 (左上角的行索引)
    // + (c/3) 将列方向大块索引转换为 0, 1, 2 (左上角的列索引)
    // 最终得到的 box_idx 0-8 是一个扁平化的索引，用于访问 box_used 数组。
    int box_idx = (r / 3) * 3 + (c / 3); 

    // 如果当前单元格 board[r][c] 已经有数字（不是 '.'），
    // 则直接跳过此单元格，递归到下一个单元格。
    if (board[r][c] != '.') {
        return dfs_solve(board, cell_idx + 1);
    }

    // 如果当前单元格是空的 ('.')，尝试填充数字 1-9
    for (int num = 1; num <= 9; ++num) { // num 对应数字 1-9
        // 剪枝优化：检查数字 num 是否可以在当前 (r, c) 单元格合法填充。
        // 即检查它是否在行 r，列 c，以及对应的 3x3 小方格中出现过。
        if (!row_used[r][num] && !col_used[c][num] && !box_used[box_idx][num]) {
            // 如果数字 num 合法，则尝试填充它：
            board[r][c] = (char)('0' + num); // 填充单元格 (char) 转换
            row_used[r][num] = true;         // 更新行标记
            col_used[c][num] = true;         // 更新列标记
            box_used[box_idx][num] = true;   // 更新小方格标记

            // 递归调用 dfs_solve，尝试填充下一个单元格。
            if (dfs_solve(board, cell_idx + 1)) {
                // 如果递归调用返回 true，说明当前路径找到了一个完整的解。
                // 立即向上返回 true，无需再尝试其他数字或回溯。
                return true;
            }

            // 回溯步骤：如果递归调用返回 false，说明当前填充的数字 num 
            // 无法导致一个完整的解。因此，撤销之前的操作，恢复状态：
            board[r][c] = '.';               // 清空当前单元格
            row_used[r][num] = false;        // 恢复行标记
            col_used[c][num] = false;        // 恢复列标记
            box_used[box_idx][num] = false;  // 恢复小方格标记
        }
    }

    // 如果尝试了所有可能的数字 1-9，都无法在当前单元格找到一个导致解的路径，
    // 则返回 false，通知上一层递归进行回溯。
    return false;
}

// --- 主函数：数独求解入口 ---
// board: 数独棋盘， char** 表示 char* 的数组
// boardSize: 棋盘的行数 (通常为 9)，此参数在此实现中冗余，直接使用常量 9
// boardColSize: 棋盘的列数 (通常为 9)，此参数在此实现中冗余，直接使用常量 9
void solveSudoku(char** board, int boardSize, int *boardColSize)
{
    // C 语言中，没有构造函数，需要手动初始化全局静态数组。
    // 在 solveSudoku 调用前，确保这些数组是干净的。
    // memset 初始化所有为 false/0
    // sizeof(row_used) 获取整个数组的字节大小
    memset(row_used, 0, sizeof(row_used));
    memset(col_used, 0, sizeof(col_used));
    memset(box_used, 0, sizeof(box_used));

    // 预处理棋盘上已有的数字，填充标记数组。
    // 这样在 DFS 过程中就无需再次检查这些固定数字，可以直接跳过。
    for (int r = 0; r < 9; ++r) {
        for (int c = 0; c < 9; ++c) {
            if (board[r][c] != '.') {
                int num = board[r][c] - '0'; // 将 '1'-'9' 映射到 1-9 的数字
                int box_idx = (r / 3) * 3 + (c / 3); // 计算 3x3 小方格的索引

                row_used[r][num] = true;
                col_used[c][num] = true;
                box_used[box_idx][num] = true;
            }
        }
    }

    // 从棋盘的第一个单元格 (线性索引 0) 开始进行深度优先搜索。
    // 如果找到解，函数会返回 true，并修改 board。
    dfs_solve(board, 0);
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    // 使用类成员变量作为标记数组，避免在递归时频繁传递和拷贝。
    // bool row[i][t]：第 i 行是否已存在数字 t+1
    // bool col[j][t]：第 j 列是否已存在数字 t+1
    // bool cell[box_row][box_col][t]：对应的 3x3 小方格是否已存在数字 t+1
    bool row[9][9];
    bool col[9][9];
    bool cell[3][3][9];

    void solveSudoku(std::vector<std::vector<char>>& board) {
        // 初始化所有标记数组为 false。
        // memset 比循环初始化更快，因为它在编译时就知道数组大小。
        std::memset(row, 0, sizeof(row));
        std::memset(col, 0, sizeof(col));
        std::memset(cell, 0, sizeof(cell));

        // 预处理棋盘上已有的数字，填充标记数组。
        // 这样在 DFS 过程中就无需再次检查这些固定数字，可以直接跳过。
        for (int i = 0; i < 9; ++i) {
            for (int j = 0; j < 9; ++j) {
                if (board[i][j] != '.') {
                    int num_idx = board[i][j] - '1'; // 将 '1'-'9' 映射到 0-8 的索引
                    row[i][num_idx] = true;
                    col[j][num_idx] = true;
                    cell[i / 3][j / 3][num_idx] = true; // 计算 3x3 小方格的索引
                }
            }
        }

        // 从棋盘的第一个单元格 (0, 0) 开始进行深度优先搜索。
        // 如果找到解，函数会返回 true，并修改 board。
        dfs(board, 0, 0);
    }

private:
    // 深度优先搜索（回溯）函数
    // board: 数独棋盘的引用，以便直接修改
    // x: 当前要考虑填充的单元格的行索引
    // y: 当前要考虑填充的单元格的列索引
    // 返回值：bool 类型，表示是否成功找到了一个解
    bool dfs(std::vector<std::vector<char>>& board, int x, int y) {
        // 如果列索引 y 达到 9，表示当前行已经处理完毕，移动到下一行的开头。
        if (y == 9) {
            x++;      // 移动到下一行
            y = 0;    // 列索引重置为 0
        }
        // 终止条件：如果行索引 x 达到 9，表示整个棋盘已遍历完毕，所有空格都已成功填充。
        if (x == 9) { 
            return true; // 找到了一个有效的数独解
        }

        // 如果当前单元格 board[x][y] 已经有一个数字（不是 '.'），
        // 则直接跳过此单元格，递归到下一个单元格。
        if (board[x][y] != '.') {
            return dfs(board, x, y + 1);
        }

        // 如果当前单元格是空的 ('.')，尝试填充数字 1-9。
        for (int num_idx = 0; num_idx < 9; ++num_idx) { // num_idx 对应数字索引 0-8
            // 剪枝优化：检查数字 num_idx+1 是否可以在当前 (x, y) 单元格合法填充。
            // 检查行 x，列 y，以及对应的 3x3 小方格中，该数字是否已经存在。
            if (!row[x][num_idx] && !col[y][num_idx] && !cell[x / 3][y / 3][num_idx]) {
                // 如果数字 num_idx+1 合法，则尝试填充它：
                board[x][y] = (char)('1' + num_idx); // 填充单元格
                row[x][num_idx] = true;              // 更新行标记
                col[y][num_idx] = true;              // 更新列标记
                cell[x / 3][y / 3][num_idx] = true;  // 更新小方格标记

                // 递归调用 dfs，尝试填充下一个单元格。
                if (dfs(board, x, y + 1)) {
                    // 如果递归调用返回 true，说明当前路径找到了一个完整的解。
                    // 立即向上返回 true，无需再尝试其他数字或回溯。
                    return true;
                }

                // 回溯步骤：如果递归调用返回 false，说明当前填充的数字 num_idx+1 
                // 无法导致一个完整的解。因此，撤销之前的操作，恢复状态：
                board[x][y] = '.';                 // 清空当前单元格
                row[x][num_idx] = false;           // 恢复行标记
                col[y][num_idx] = false;           // 恢复列标记
                cell[x / 3][y / 3][num_idx] = false; // 恢复小方格标记
            }
        }

        // 如果尝试了所有可能的数字 1-9，都无法在当前单元格找到一个导致解的路径，
        // 则返回 false，通知上一层递归进行回溯。
        return false;
    }
};
```

# Question

[n-queens-ii](https://leetcode.cn/problems/n-queens-ii/)



# Answer

> solution

1.  **第一步永远是简化数据结构。**
    *   为了快速判断某个位置是否可以放置皇后，我们不能每次都遍历之前已放置的皇后。
    *   需要三个标记数组（或位掩码）：
        *   `col[j]`：标记第 `j` 列是否已被占用。
        *   `diag1[k]`：标记主对角线 `row - col = 常数` 是否已被占用。`k` 的计算通常是 `row - col + N - 1`，将负数索引映射到非负数。
        *   `diag2[k]`：标记副对角线 `row + col = 常数` 是否已被占用。`k` 的计算通常是 `row + col`。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   整个解决方案围绕一个递归的深度优先搜索（DFS）函数。
    *   DFS 函数的参数是当前处理的 `行号 (row)`。
    *   基本情况：如果 `row == N`，说明所有皇后都已放置，找到一个解，计数器加一，然后返回。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   在每一行 `row` 中，从第一列 `col = 0` 遍历到 `col = N-1`。
    *   对于每个位置 `(row, col)`，检查三个条件：
        *   `col[col]` 是否为 `true` (列是否被占用)
        *   `diag1[row - col + N - 1]` 是否为 `true` (主对角线是否被占用)
        *   `diag2[row + col]` 是否为 `true` (副对角线是否被占用)
    *   如果这三个条件都为 `false`，说明当前位置 `(row, col)` 可以放置皇后。
        *   **放置：** 将 `col[col]`, `diag1[...]`, `diag2[...]` 设为 `true`。
        *   **递归：** 调用 DFS 函数处理下一行 `row + 1`。
        *   **回溯：** 递归返回后，将 `col[col]`, `diag1[...]`, `diag2[...]` 设回 `false`，为当前行 `row` 的下一个列尝试腾出位置。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   这个解决方案是纯计数，不影响任何外部状态。
    *   C 语言版本需要手动 `malloc`/`free` 来避免内存泄漏。
    *   C++ 版本使用 `std::vector<bool>` 可以自动管理内存，更安全。

**总结来说，核心思路就是：**

**回溯法 + 冲突优化。**

*   **回溯法 (DFS)：** 逐行放置皇后。每放置一个皇后，就尝试放置下一行的皇后。如果当前行无法放置（所有列都冲突），或者下一行递归返回，则撤销当前行的放置，尝试当前行的下一个列。
*   **冲突优化：** 使用三个布尔数组（或位掩码）`col`, `diag1`, `diag2`，以 O(1) 的时间复杂度检查某个位置是否被其他皇后攻击，避免了每次 O(N) 的线性遍历检查。这是解决 N 皇后问题高效的关键。

### Way Of C

> source code

```c
// 使用全局变量来避免频繁传递，或者作为结构体成员传递
static int N_QUEENS_N;
static int N_QUEENS_COUNT;
static bool *cols; // 记录列是否被占用
static bool *diag1; // 记录主对角线 (row - col + N - 1) 是否被占用
static bool *diag2; // 记录副对角线 (row + col) 是否被占用

static void solve_n_queens_c(int row) {
    if (row == N_QUEENS_N) {
        N_QUEENS_COUNT++;
        return;
    }

    for (int col = 0; col < N_QUEENS_N; col++) {
        // 检查当前位置 (row, col) 是否冲突
        // 主对角线索引: row - col + N - 1
        // 副对角线索引: row + col
        if (!cols[col] && !diag1[row - col + N_QUEENS_N - 1] && !diag2[row + col]) {
            // 放置皇后
            cols[col] = true;
            diag1[row - col + N_QUEENS_N - 1] = true;
            diag2[row + col] = true;

            // 递归到下一行
            solve_n_queens_c(row + 1);

            // 回溯，移除皇后
            cols[col] = false;
            diag1[row - col + N_QUEENS_N - 1] = false;
            diag2[row + col] = false;
        }
    }
}

int totalNQueens(int n) {
    if (n <= 0) {
        return 0; // 无效输入
    }
    N_QUEENS_N = n;
    N_QUEENS_COUNT = 0;

    // 分配内存
    cols = (bool *)malloc(n * sizeof(bool));
    diag1 = (bool *)malloc((2 * n - 1) * sizeof(bool));
    diag2 = (bool *)malloc((2 * n - 1) * sizeof(bool));

    if (!cols || !diag1 || !diag2) {
        // 内存分配失败
        free(cols);
        free(diag1);
        free(diag2);
        return 0; // 或者处理错误
    }

    // 初始化所有标记为 false
    for (int i = 0; i < n; i++) cols[i] = false;
    for (int i = 0; i < (2 * n - 1); i++) {
        diag1[i] = false;
        diag2[i] = false;
    }

    solve_n_queens_c(0);

    // 释放内存
    free(cols);
    free(diag1);
    free(diag2);

    return N_QUEENS_COUNT;
}
```

---

### Way Of C++

> source code

```c++
#include <vector> // for std::vector

class Solution {
public:
    int totalNQueens(int n) {
        if (n <= 0) {
            return 0;
        }
        
        // 使用 vector<bool> 比原始的 bool* 更安全和方便
        // col[j] 记录第 j 列是否被占用
        // diag1[i - j + n - 1] 记录主对角线 (i - j) 是否被占用
        // diag2[i + j] 记录副对角线 (i + j) 是否被占用
        std::vector<bool> col(n, false);
        std::vector<bool> diag1(2 * n - 1, false); // i - j 的范围是 -(n-1) 到 (n-1)，需要偏移 n-1
        std::vector<bool> diag2(2 * n - 1, false); // i + j 的范围是 0 到 2n-2

        int count = 0;
        solveNQueensDFS(n, 0, col, diag1, diag2, count);
        return count;
    }

private:
    void solveNQueensDFS(int n, 
                         int row, 
                         std::vector<bool>& col, 
                         std::vector<bool>& diag1, 
                         std::vector<bool>& diag2, 
                         int& count) {
        if (row == n) {
            count++;
            return;
        }

        for (int j = 0; j < n; ++j) { // 遍历当前行的所有列
            // 检查当前位置 (row, j) 是否冲突
            if (!col[j] && !diag1[row - j + n - 1] && !diag2[row + j]) {
                // 放置皇后
                col[j] = true;
                diag1[row - j + n - 1] = true;
                diag2[row + j] = true;

                // 递归到下一行
                solveNQueensDFS(n, row + 1, col, diag1, diag2, count);

                // 回溯，移除皇后
                col[j] = false;
                diag1[row - j + n - 1] = false;
                diag2[row + j] = false;
            }
        }
    }
};
```

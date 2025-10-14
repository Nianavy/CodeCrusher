# Question

[n-queens](https://leetcode.cn/problems/n-queens/)



# Answer

> solution

经典的**回溯算法（Backtracking）**来解决 N 皇后问题，并且都使用了**O(1) 冲突检查**的优化手段。它们的主要区别在于语言特性带来的实现细节差异。

### 核心解决思路：回溯算法 + O(1) 冲突检查

1.  **逐行放置皇后：**
    *   算法从棋盘的第一行开始（行 `0`）。
    *   在每一行中，它会遍历所有可能的列，尝试放置一个皇后。

2.  **冲突检查（关键优化）：**
    *   为了判断在 `(row, col)` 位置放置皇后是否合法，需要检查：
        *   当前列 `col` 是否已被其他皇后占用。
        *   当前位置 `(row, col)` 所在的主对角线（从左上到右下）是否已被占用。
        *   当前位置 `(row, col)` 所在的副对角线（从右上到左下）是否已被占用。
    *   **O(1) 优化：** 两个方案都使用了布尔数组（或 C 中的 `int` 数组模拟 `bool`）来记录这些占用状态。
        *   `columns[col]`：标记列 `col` 是否被占用。
        *   `diagonals1[row - col + (n-1)]` 或 `dg[u - i + n]`：标记主对角线是否被占用。通过 `row - col` 获得对角线索引，加上 `n-1` (C版本) 或 `n` (C++版本) 来确保索引非负，并将其映射到唯一的数组索引上。
        *   `diagonals2[row + col]` 或 `udg[u + i]`：标记副对角线是否被占用。`row + col` 天然是唯一的非负索引。
    *   通过这些数组，冲突检查可以在常数时间内完成，避免了每次都遍历已放置皇后来检查冲突的 O(N) 成本。

3.  **递归与回溯：**
    *   **放置皇后：** 如果在 `(row, col)` 找到一个安全位置，就放置皇后（在 `path` 或 `board` 中标记 'Q'），并更新相应的 `columns`、`diagonals1`、`diagonals2` 数组为已占用。
    *   **递归：** 成功放置皇后后，算法递归调用自身，处理下一行 (`row + 1`)。
    *   **终止条件：** 当 `row` 达到 `n`（即成功在所有 `n` 行都放置了皇后）时，说明找到了一个完整的解决方案。此时，将当前的棋盘状态保存起来。
    *   **回溯：** 递归调用返回后（无论是找到了解决方案还是当前路径无解），必须**撤销**前一步的放置。这意味着要将 `path` 或 `board` 中的 'Q' 变回 '.'，并解除 `columns`、`diagonals1`、`diagonals2` 中相应位置的占用标记。这是确保算法能探索所有可能路径的关键。

### 两个方案的异同

#### 共同点：

*   **算法核心：** 都是基于回溯（深度优先搜索）和 O(1) 冲突检查。
*   **状态表示：** 都使用了三个布尔数组（或 `int` 数组）来跟踪列、主对角线和副对角线的占用情况。
*   **对角线索引计算：** 都采用 `row - col + offset` 和 `row + col` 的方式。

#### 不同点：

1.  **语言特性和数据结构：**
    *   **C++ 版本：** 充分利用 C++ 的 `std::vector<bool>` 和 `std::vector<std::string>`，这使得内存管理和字符串操作变得非常方便和安全。对象的成员变量用于存储全局状态（`n`, `col`, `dg`, `udg`, `ans`, `path`），简化了函数参数传递。
    *   **C 版本：** 必须手动使用 `malloc` 和 `free` 进行内存管理。`int` 数组被用来模拟布尔数组。由于 C 没有类的概念，`solutionsSize` 和其他状态数组需要声明为 `static` 全局变量，或者通过复杂的指针传递来模拟。

2.  **解决方案的存储与返回：**
    *   **C++ 版本：** 使用 `std::vector<std::vector<std::string>>` 来收集所有解决方案，`push_back` 即可。返回值是 `std::vector`，由 C++ 运行时负责清理。
    *   **C 版本：**
        *   `solutionsSize` 是一个 `static` 全局变量。
        *   `solutions` (`char***`) 是动态分配的，需要手动 `realloc` 来扩展容量，或者像你提供的代码那样预估一个足够大的固定容量。
        *   `generateBoard` 每次找到解决方案时都会动态分配内存来创建棋盘的字符串表示。
        *   函数 `solveNQueens` 的签名要求返回 `char***`，并且 `returnSize` 和 `returnColumnSizes` 都需要通过指针返回，这些都增加了手动内存管理的复杂性。调用者必须负责 `free` 所有动态分配的内存。

3.  **内存管理与安全性：**
    *   **C++ 版本：** 更加安全，`std::vector` 自动处理内存的分配、释放和动态增长，减少了内存泄漏和溢出的风险。
    *   **C 版本：** 风险较高。代码中对 `malloc` 的返回值没有检查（可能返回 `NULL`），并且 `solutions` 数组的初始容量是固定的 `501`，如果 `n` 较大导致解决方案数量超过这个值，将导致缓冲区溢出。调用者不 `free` 所有返回的内存会导致内存泄漏。

### 总结：

C++ 版本在**代码简洁性、安全性**和**符合现代编程实践**方面表现出色。它利用了语言和标准库的优势，将精力集中在算法逻辑本身，而不是底层的内存管理。

C 版本则展示了在资源受限或需要极致控制内存时的实现方式，但它也暴露了手动内存管理带来的**复杂性和潜在风险**。需要更严格的错误处理（`malloc` 检查）和更精细的容量管理（`realloc` 或预先计数）才能达到生产级别的健壮性。

在解决此类算法问题时，如果语言允许，优先选择像 C++ 版本那样，利用高级数据结构和自动内存管理机制，能写出更少 Bug、更易维护的代码。

### Way Of C

> source code

```c
// 全局变量，用于记录找到的解决方案数量
// 声明为全局是为了在 backtrack 函数中直接修改，避免通过多层指针传递
static int solutionsSize;

// 辅助函数：根据皇后位置数组生成棋盘的字符串表示
// queens: 存储每行皇后所在列的数组，queens[i] 表示第 i 行皇后在 queens[i] 列
// n: 棋盘大小
// 返回值: 动态分配的 char** 类型的棋盘，每行是一个字符串
char** generateBoard(int* queens, int n) {
    // 为 n 行分配 char* 指针
    char** board = (char**)malloc(sizeof(char*) * n);
    // 遍历每一行
    for (int i = 0; i < n; i++) {
        // 为当前行分配 (n+1) 个字符空间，+1 用于存储字符串结束符 '\0'
        board[i] = (char*)malloc(sizeof(char) * (n + 1));
        // 将当前行所有位置初始化为 '.' (空)
        for (int j = 0; j < n; j++) board[i][j] = '.';
        // 在皇后所在的列放置 'Q'
        board[i][queens[i]] = 'Q';
        // 添加字符串结束符
        board[i][n] = 0; // 或 '\0'
    }
    return board;
}

// 核心回溯函数，用于搜索所有 N 皇后问题的解决方案
// solutions: 存储所有解决方案的指针数组 (char***)
// queens: 记录当前已放置皇后位置的数组，queens[row] 存储第 row 行皇后所在的列
// n: 棋盘大小
// row: 当前正在尝试放置皇后的行号
// columns: 布尔数组，标记哪些列已被占用 (columns[col] == true 表示 col 列被占用)
// diagonals1: 布尔数组，标记哪些主对角线已被占用 (diagonals1[row - col + n - 1] == true 表示该对角线被占用)
// diagonals2: 布尔数组，标记哪些副对角线已被占用 (diagonals2[row + col] == true 表示该对角线被占用)
void backtrack(char*** solutions, int* queens, int n, int row, int* columns, int* diagonals1, int* diagonals2) {
    // 递归终止条件：如果已成功在所有 n 行放置了皇后
    if (row == n) {
        // 生成当前皇后位置对应的棋盘表示
        char** board = generateBoard(queens, n);
        // 将生成的棋盘添加到解决方案列表中
        solutions[solutionsSize++] = board;
    } else {
        // 遍历当前行 (row) 的每一列 (i)，尝试放置皇后
        for (int i = 0; i < n; i++) {
            // 检查当前列是否已被占用
            if (columns[i]) {
                continue; // 如果已占用，尝试下一列
            }
            // 计算当前位置 (row, i) 对应的主对角线索引
            // 索引计算方式：row - i + (n - 1) 确保索引非负且唯一
            int diagonal1 = row - i + n - 1;
            // 检查主对角线是否已被占用
            if (diagonals1[diagonal1]) {
                continue; // 如果已占用，尝试下一列
            }
            // 计算当前位置 (row, i) 对应的副对角线索引
            // 索引计算方式：row + i 确保索引唯一
            int diagonal2 = row + i;
            // 检查副对角线是否已被占用
            if (diagonals2[diagonal2]) {
                continue; // 如果已占用，尝试下一列
            }

            // 如果当前位置 (row, i) 安全，则放置皇后
            queens[row] = i; // 记录第 row 行皇后在 i 列
            columns[i] = true; // 标记第 i 列被占用
            diagonals1[diagonal1] = true; // 标记对应主对角线被占用
            diagonals2[diagonal2] = true; // 标记对应副对角线被占用

            // 递归调用，尝试在下一行放置皇后
            backtrack(solutions, queens, n, row + 1, columns, diagonals1, diagonals2);

            // 回溯：撤销当前放置的皇后，恢复状态
            queens[row] = -1; // 移除第 row 行的皇后标记
            columns[i] = false; // 释放第 i 列
            diagonals1[diagonal1] = false; // 释放对应主对角线
            diagonals2[diagonal2] = false; // 释放对应副对角线
        }
    }
}

// 主函数：解决 N 皇后问题
// n: 棋盘大小
// returnSize: 指向一个 int 的指针，用于返回找到的解决方案总数
// returnColumnSizes: 指向一个 int* 的指针，用于返回每个解决方案中行的大小 (在此问题中，所有行大小均为 n)
// 返回值: 包含所有解决方案的 char*** 数组
char*** solveNQueens(int n, int* returnSize, int** returnColumnSizes) {
    // 动态分配一个数组来存储所有解决方案，初始容量为 501 (一个估算值，可能需要根据 N 动态调整)
    char*** solutions = (char***)malloc(sizeof(char**) * 501);
    // 初始化解决方案计数器
    solutionsSize = 0;

    // 声明并初始化存储皇后位置的数组，queens[i] 存储第 i 行皇后的列号
    // 用 -1 初始化表示该行尚未放置皇后
    int queens[n];
    memset(queens, -1, sizeof(queens)); // sizeof(queens) 是 n * sizeof(int)

    // 声明并初始化布尔数组，用于 O(1) 检查列、主对角线、副对角线是否被占用
    // columns: 大小为 n，用于列
    // diagonals1: 大小为 2n-1，用于主对角线
    // diagonals2: 大小为 2n-1，用于副对角线
    int columns[n];
    int diagonals1[n + n]; // (n-1) + (n-1) + 1 = 2n-1, 这里用 n+n 足够
    int diagonals2[n + n]; // 同上
    memset(columns, 0, sizeof(columns)); // 0 表示未占用 (false)
    memset(diagonals1, 0, sizeof(diagonals1));
    memset(diagonals2, 0, sizeof(diagonals2));

    // 调用回溯函数开始搜索解决方案
    backtrack(solutions, queens, n, 0, columns, diagonals1, diagonals2);

    // 设置返回给调用者的解决方案总数
    *returnSize = solutionsSize;
    // 为 returnColumnSizes 分配内存，存储每个解决方案的行大小 (即 n)
    *returnColumnSizes = (int*)malloc(sizeof(int) * solutionsSize);
    // 填充 returnColumnSizes 数组，每个解决方案的行数都是 n
    for (int i = 0; i < solutionsSize; i++) {
        (*returnColumnSizes)[i] = n;
    }
    // 返回包含所有解决方案的数组
    return solutions;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int n; // ✅ 成员变量存储 n
    vector<bool> col, dg, udg; // ✅ 使用布尔数组进行 O(1) 冲突检查，这是最经典且高效的优化
    vector<vector<string>> ans; // ✅ 存储所有解决方案
    vector<string> path; // ✅ 存储当前解决方案的棋盘状态

    vector<vector<string>> solveNQueens(int _n) {
        n = _n;
        col = vector<bool>(n); // 列占用
        // 主对角线 dg 索引: row - col + n。加 n 避免负数，确保索引在 [0, 2n-1]
        // 副对角线 udg 索引: row + col。确保索引在 [0, 2n-2]
        dg = udg = vector<bool>(n * 2); // ✅ 对角线占用，大小为 2n 足够覆盖所有索引
        path = vector<string>(n, string(n, '.')); // 初始化棋盘

        dfs(0); // 从第一行开始 DFS
        return ans;
    }

    void dfs(int u) { // u 表示当前行
        if (u == n) { // ✅ 成功放置 n 个皇后，找到一个解决方案
            ans.push_back(path);
            return;
        }

        for (int i = 0; i < n; i ++ ) { // 尝试在当前行 u 的每一列 i 放置皇后
            // ✅ 检查当前位置 (u, i) 是否与列、主对角线、副对角线冲突
            if (!col[i] && !dg[u - i + n] && !udg[u + i]) {
                // ✅ 放置皇后，标记占用状态
                col[i] = dg[u - i + n] = udg[u + i] = true;
                path[u][i] = 'Q';
                dfs(u + 1); // 递归到下一行
                // ✅ 回溯，撤销标记状态
                path[u][i] = '.';
                col[i] = dg[u - i + n] = udg[u + i] = false;
            }
        }
    }
};
```

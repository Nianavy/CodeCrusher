# Question

[word-search](https://leetcode.cn/problems/word-search/)



# Answer

> solution

单词搜索 (DFS/回溯)

### 1. 核心挑战与策略

*   **挑战**：在 $O(N \cdot M \cdot 4^L)$（$L$ 是单词长度）的最坏情况下，防止 DFS 陷入循环，同时最小化内存开销。
*   **策略**：**深度优先搜索 (DFS) + 原地修改标记。**

### 2. 状态管理： $O(1)$ 空间优化

在回溯搜索中，最关键的是如何高效地标记一个单元格**在当前路径中**是否已被访问。

*   **传统方法**：使用一个额外的 $O(R \cdot C)$ 布尔数组 `visited`。每次 DFS 结束，需要重置状态。
*   **Linus 优化（原地修改）**：我们避免使用额外的数组。
    1.  **标记**：在进入 DFS 之前，将当前单元格 $board[r][c]$ 的值**临时修改**为一个不可能出现在单词中的特殊字符（例如 `#` 或 `.`）。
    2.  **检查**：在搜索邻居时，直接检查 $board[next\_r][next\_c]$ 是否为特殊标记。
    3.  **回溯**：在 DFS 函数返回之前（无论成功与否），**必须**将 $board[r][c]$ 恢复为它的原始值。

这种方法将状态存储的复杂性转移到了数据结构本身，实现了 $O(1)$ 额外空间（忽略递归栈）。

### 3. DFS/回溯的决策流程

DFS 函数的主要任务是判断从当前单元格 $(r, c)$ 开始，是否能找到单词的剩余部分。

1.  **匹配检查**：首先判断 $board[r][c]$ 是否等于 `word` 中当前索引 $u$ 处的字符。如果不匹配，立即返回 `false`。
2.  **终止条件**：如果 $u$ 是单词的最后一个索引，且字符匹配，则返回 `true`。
3.  **探索邻居**：使用一个包含四个方向变化的数组（`dx`, `dy` 或 `DR`, `DC`）来循环遍历上下左右四个邻居。
    *   对于每个邻居，执行严格的**三重检查**：
        a. 边界检查（是否越界）。
        b. 访问检查（是否被标记为已访问）。
        c. 递归调用 DFS。
    *   如果任何一个递归调用返回 `true`，则整个搜索成功，立即返回 `true`。
4.  **状态恢复**：如果在所有邻居中都没有找到路径，则必须恢复 $board[r][c]$ 的原始值，然后返回 `false`。

### 4. C 语言的工程考量

*   **字符串处理**：在 C 版本中，可以使用 `strlen(word)` 来确定终止条件，并传递 `current_word_idx + 1` 来跟踪进度，而不是像传统 C 风格那样传递 `word + 1`，因为后者不利于获取总长度。
*   **内存安全**：由于我们采用了 $O(1)$ 原地修改优化，避免了 `malloc` 额外的 `visited` 数组，因此解决了 C 语言实现中常见的内存泄漏问题。

### Way Of C

> source code

```c
// 四个方向：上，右，下，左 (对应 (r, c) 变化)
static const int DR[4] = {-1, 0, 1, 0};
static const int DC[4] = {0, 1, 0, -1};

/**
 * 深度优先搜索 (回溯)
 * 使用原地修改 (将 board[r][c] 改为 '#' 或其他特殊字符) 来标记访问。
 */
static bool dfs_c(char* word, char** board, int R, int C, int current_word_idx, int r, int c)
{
    // 1. 检查字符是否匹配
    // 为什么不使用 *word 来匹配？因为我们需要知道 word 的长度来判断终止条件。
    if (board[r][c] != word[current_word_idx]) {
        return false;
    }

    // 2. 终止条件：找到整个单词
    if (current_word_idx == strlen(word) - 1) {
        return true;
    }

    // 3. 标记访问 (原地修改)
    char original_char = board[r][c];
    board[r][c] = '#'; 

    // 4. 探索四个方向
    for (int i = 0; i < 4; ++i) {
        int next_r = r + DR[i];
        int next_c = c + DC[i];

        // 边界检查 and 排除已访问的标记 ('#')
        if (next_r >= 0 && next_r < R && 
            next_c >= 0 && next_c < C && 
            board[next_r][next_c] != '#') 
        {
            if (dfs_c(word, board, R, C, current_word_idx + 1, next_r, next_c)) {
                // 找到路径，必须恢复状态并返回
                board[r][c] = original_char;
                return true;
            }
        }
    }

    // 5. 回溯：恢复状态
    board[r][c] = original_char;
    return false;
}

/**
 * 单词搜索
 */
bool exist(char** board, int boardSize, int* boardColSize, char* word) {
    if (boardSize == 0 || boardColSize == NULL || *boardColSize == 0 || word == NULL || *word == '\0') {
        return false;
    }

    int R = boardSize;
    int C = boardColSize[0]; // 假设矩形矩阵

    // 外部循环：尝试每一个起始点
    for (int r = 0; r < R; ++r) {
        for (int c = 0; c < C; ++c) {
            if (dfs_c(word, board, R, C, 0, r, c)) {
                return true;
            }
        }
    }

    return false;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
private:
    int R, C;
    int dx[4] = {-1, 0, 1, 0}; // 北, 东, 南, 西
    int dy[4] = {0, 1, 0, -1};

    bool dfs(vector<vector<char>>& board, const string& word, int u, int x, int y) {
        
        // 1. 检查字符是否匹配
        if (board[x][y] != word[u]) {
            return false;
        }
        
        // 2. 终止条件：找到整个单词
        if (u == word.size() - 1) {
            return true;
        }

        // 3. 标记访问 (原地修改优化)
        char original_char = board[x][y];
        board[x][y] = '#'; // 使用 # 标记已访问

        // 4. 探索四个方向
        for (int i = 0; i < 4; ++i) {
            int next_x = x + dx[i];
            int next_y = y + dy[i];

            // 边界检查 and 排除已访问的标记
            if (next_x >= 0 && next_x < R && next_y >= 0 && next_y < C && board[next_x][next_y] != '#') {
                if (dfs(board, word, u + 1, next_x, next_y)) {
                    // 如果找到路径，立即返回 true
                    board[x][y] = original_char; // 路径已经找到，但必须恢复board状态
                    return true;
                }
            }
        }
        
        // 5. 回溯：恢复状态
        board[x][y] = original_char;
        return false;
    }

public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || word.empty()) return false;
        R = board.size();
        C = board[0].size();

        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                if (dfs(board, word, 0, i, j)) {
                    return true;
                }
            }
        }
        return false;
    }
};
```

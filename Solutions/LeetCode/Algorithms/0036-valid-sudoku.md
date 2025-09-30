# Question

[valid-sudoku](https://leetcode.cn/problems/valid-sudoku/)



# Answer

> solution

解决这个问题的核心思路是：**将数独的验证规则分解为三个独立的、且可以通过简单循环遍历完成的检查任务（行、列、3x3 小方格）**。对于每个检查任务，都使用一个临时的、固定大小的布尔数组（或哈希集）作为**“记忆体”**，来跟踪数字 1-9 的出现情况。一旦在当前检查范围内发现任何数字重复，就立即判断数独无效。

### **验证数独棋盘有效性 (isValidSudoku) 的解决思路总结**

验证一个数独棋盘是否有效，需要遵循数独的三个基本规则：

1.  **行规则**：每行包含的数字 1-9 必须互不相同。
2.  **列规则**：每列包含的数字 1-9 必须互不相同。
3.  **3x3 小方格规则**：每个 3x3 的小方格内包含的数字 1-9 必须互不相同。

空单元格 (`.`) 不参与这些规则的验证，它可以是任何数字。

**核心解决思路分解：**

该问题可以通过对棋盘进行三次独立的遍历来解决，每次遍历检查一个规则：

1.  **数据结构准备：用于跟踪重复数字**
    *   对于每一次独立的检查（一行、一列或一个小方格），我们需要一个临时的机制来记录数字 1-9 是否已经出现过。
    *   最简单高效的方法是使用一个**布尔数组 (或字符数组在 C 中模拟布尔)**，大小为 `10`（索引 `0` 忽略，索引 `1` 到 `9` 对应数字 '1' 到 '9'）。
    *   在每次开始检查新的行/列/小方格时，都将这个布尔数组**重置**为所有 `false`（或 `0`）。

2.  **第一次遍历：检查所有行**
    *   外层循环遍历棋盘的每一行（从 `row = 0` 到 `8`）。
    *   在每次外层循环开始时，**重置**布尔数组。
    *   内层循环遍历当前行的每个单元格（从 `col = 0` 到 `8`）。
        *   如果当前单元格 `board[row][col]` 不是 `.` (空单元格)：
            *   将字符数字转换为对应的数字索引（例如 `char_digit - '0'` 得到 `1-9`）。
            *   检查布尔数组中该数字对应的位置是否已为 `true`。
                *   如果**是**：说明该数字在当前行中重复，数独**无效**，立即返回 `false`。
            *   如果**不是**：将布尔数组中该数字对应的位置设为 `true`。
    *   如果所有行都检查完毕且未发现重复，则继续下一步。

3.  **第二次遍历：检查所有列**
    *   外层循环遍历棋盘的每一列（从 `col = 0` 到 `8`）。
    *   在每次外层循环开始时，**重置**布尔数组。
    *   内层循环遍历当前列的每个单元格（从 `row = 0` 到 `8`）。
        *   （逻辑与检查行相同，只是访问单元格的索引是 `board[row][col]` 对应的 `board[j][i]` 或 `board[i][j]`）
        *   如果当前单元格 `board[row][col]` 不是 `.`：
            *   转换为数字索引。
            *   检查布尔数组。如果重复，返回 `false`。
            *   否则，标记为 `true`。
    *   如果所有列都检查完毕且未发现重复，则继续下一步。

4.  **第三次遍历：检查所有 3x3 小方格**
    *   外层两层循环控制 9 个 3x3 小方格的左上角起点。例如：`start_row` 从 `0, 3, 6` 取值，`start_col` 从 `0, 3, 6` 取值。
    *   在每次外层循环开始时，**重置**布尔数组。
    *   内层两层循环遍历当前 3x3 小方格内的所有单元格（例如，相对于 `start_row` 和 `start_col`，偏移 `r` 从 `0` 到 `2`，`c` 从 `0` 到 `2`）。
        *   如果当前单元格 `board[start_row + r][start_col + c]` 不是 `.`：
            *   转换为数字索引。
            *   检查布尔数组。如果重复，返回 `false`。
            *   否则，标记为 `true`。
    *   如果所有小方格都检查完毕且未发现重复，则继续下一步。

5.  **最终结果**：
    *   如果所有三个检查都通过（即在任何阶段都没有返回 `false`），则说明数独棋盘**有效**，返回 `true`。

**核心思想总结：**

这种方法将复杂规则分解，通过局部状态（布尔数组）在**常数时间 (O(1))** 内完成验证。由于数独棋盘大小是固定的 9x9，无论循环多少次，总的运算量都是常数。这种思路清晰、直接，并且高效。

### Way Of C

> source code

```c
// 主函数：验证数独棋盘的有效性
// board: 数独棋盘， char** 是 char* 的数组
// boardSize: 棋盘的行数 (通常为 9)
// boardColSize: 棋盘的列数 (通常为 9，此处为冗余参数，不再使用)
// 注意：对于 9x9 数独，boardSize 和 boardColSize 实际都是 9，
//       因此在函数内部直接使用常量 9 更简洁。
bool isValidSudoku(char** board, int boardSize, int* boardColSize) // boardColSize 实际上是冗余的
{
    // C 语言中，布尔数组可以使用 char 数组来实现，0 表示 false，非 0 (通常是 1) 表示 true。
    // mark[0] 不用，mark[1] 对应数字 '1'，mark[9] 对应数字 '9'。
    char mark[10]; // 栈上分配，高效

    // 1. 检查所有行
    for (int i = 0; i < 9; ++i) { // 遍历行索引 0-8
        memset(mark, 0, sizeof(mark)); // 每检查新的一行前，重置标记数组为全 false
        for (int j = 0; j < 9; ++j) { // 遍历列索引 0-8
            if (board[i][j] != '.') { // 如果单元格不是空的
                // 将字符数字 '1'-'9' 转换为索引 1-9 (因为 mark 数组大小是 10)
                int num_idx = board[i][j] - '0'; 
                // 如果该数字已经出现过 (mark[num_idx] 为 true)，则数独无效
                if (mark[num_idx]) {
                    return false;
                }
                mark[num_idx] = 1; // 标记该数字已出现
            }
        }
    }

    // 2. 检查所有列
    for (int j = 0; j < 9; ++j) { // 遍历列索引 0-8
        memset(mark, 0, sizeof(mark)); // 每检查新的一列前，重置标记数组为全 false
        for (int i = 0; i < 9; ++i) { // 遍历行索引 0-8 (注意这里是 board[i][j])
            if (board[i][j] != '.') { // 如果单元格不是空的
                int num_idx = board[i][j] - '0'; // 将 '1'-'9' 映射到索引 1-9
                // 如果该数字已经出现过，则数独无效
                if (mark[num_idx]) {
                    return false;
                }
                mark[num_idx] = 1; // 标记该数字已出现
            }
        }
    }

    // 3. 检查所有 3x3 小方格
    // 外层循环控制 3x3 方格的左上角起点 (start_row, start_col)
    for (int start_row = 0; start_row < 9; start_row += 3) {
        for (int start_col = 0; start_col < 9; start_col += 3) {
            memset(mark, 0, sizeof(mark)); // 每检查新的小方格前，重置标记数组为全 false
            // 内层循环遍历当前 3x3 小方格内的所有单元格
            for (int r = 0; r < 3; ++r) {
                for (int c = 0; c < 3; ++c) {
                    char cell_val = board[start_row + r][start_col + c];
                    if (cell_val != '.') { // 如果单元格不是空的
                        int num_idx = cell_val - '0'; // 将 '1'-'9' 映射到索引 1-9
                        // 如果该数字已经出现过，则数独无效
                        if (mark[num_idx]) {
                            return false;
                        }
                        mark[num_idx] = 1; // 标记该数字已出现
                    }
                }
            }
        }
    }
    
    // 所有检查都通过，数独有效
    return true;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    bool isValidSudoku(std::vector<std::vector<char>>& board) {
        // 使用 bool 数组作为哈希集，st[0] 表示数字 '1'，st[8] 表示数字 '9'。
        // 因为 board size 是 9x9 的常数，所以可以在栈上分配。
        // C++17 可以用 std::array<bool, 9> st; st.fill(false);
        bool st[9]; 

        // 1. 检查所有行
        for (int i = 0; i < 9; ++i) { // 遍历行索引
            // 每次检查新的一行前，重置标记数组
            std::memset(st, 0, sizeof(st)); 
            for (int j = 0; j < 9; ++j) { // 遍历列索引
                if (board[i][j] != '.') { // 如果单元格不是空的
                    int num_idx = board[i][j] - '1'; // 将 '1'-'9' 映射到 0-8 的索引
                    // 如果该数字已经出现过，则数独无效
                    if (st[num_idx]) {
                        return false;
                    }
                    st[num_idx] = true; // 标记该数字已出现
                }
            }
        }

        // 2. 检查所有列
        for (int j = 0; j < 9; ++j) { // 遍历列索引
            // 每次检查新的一列前，重置标记数组
            std::memset(st, 0, sizeof(st)); 
            for (int i = 0; i < 9; ++i) { // 遍历行索引 (注意这里是 board[i][j])
                if (board[i][j] != '.') { // 如果单元格不是空的
                    int num_idx = board[i][j] - '1'; // 将 '1'-'9' 映射到 0-8 的索引
                    // 如果该数字已经出现过，则数独无效
                    if (st[num_idx]) {
                        return false;
                    }
                    st[num_idx] = true; // 标记该数字已出现
                }
            }
        }

        // 3. 检查所有 3x3 小方格
        // 外层循环控制 3x3 方格的左上角起点 (start_row, start_col)
        for (int start_row = 0; start_row < 9; start_row += 3) {
            for (int start_col = 0; start_col < 9; start_col += 3) {
                // 每次检查新的小方格前，重置标记数组
                std::memset(st, 0, sizeof(st)); 
                // 内层循环遍历当前 3x3 小方格内的所有单元格
                for (int r = 0; r < 3; ++r) {
                    for (int c = 0; c < 3; ++c) {
                        char cell_val = board[start_row + r][start_col + c];
                        if (cell_val != '.') { // 如果单元格不是空的
                            int num_idx = cell_val - '1'; // 将 '1'-'9' 映射到 0-8 的索引
                            // 如果该数字已经出现过，则数独无效
                            if (st[num_idx]) {
                                return false;
                            }
                            st[num_idx] = true; // 标记该数字已出现
                        }
                    }
                }
            }
        }
        
        // 所有检查都通过，数独有效
        return true;
    }
};
```

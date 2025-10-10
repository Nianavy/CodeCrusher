# Question

[permutations](https://leetcode.cn/problems/permutations/)



# Answer

> solution

解决“全排列”问题的最佳实践是：**采用回溯算法，通过递归和状态管理来系统地构建每个排列，并在每个选择点进行“尝试-撤销”操作。**

1.  **第一步永远是简化数据结构。**
    *   **思想：** 回溯法本身就是一种简洁而强大的范式。辅助数据结构应尽可能精简，只用于记录当前路径和已使用元素。
    *   **实现：**
        *   一个 `path` (或 `stack`) 数组：用于存储当前正在构建的排列。
        *   一个 `used` (或 `st`) 数组：布尔类型，用于标记原始数组中的元素是否已经在 `path` 中被使用。
        *   `results` (或 `ans`) 数组：用于收集所有完成的排列。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   **空数组输入：** 当 `numsSize` 为 `0` 时，应该返回一个包含一个空排列的结果集 `{{}}`。
    *   **回溯终止条件：** 当 `path` 的长度达到 `numsSize` 时，意味着一个完整的排列已经构建完成，此时将其添加到 `results` 中并返回。
    *   **剪枝条件：** 在遍历选择下一个元素时，只考虑那些尚未被 `used` 数组标记的元素。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **DFS 辅助函数：** 通常定义一个递归的 `dfs` 辅助函数，它负责在不同深度（即 `path` 的不同位置）做出选择。
    *   **递归过程：**
        *   **基本情况 (Base Case)：** 如果 `path` 的长度等于 `numsSize`，说明当前 `path` 是一个完整的排列，将其复制到 `results` 中，然后返回。
        *   **选择 (Choose)：** 遍历 `nums` 数组的所有元素。对于每个元素 `nums[i]`：
            *   **检查：** 如果 `nums[i]` 还没有被 `used` 数组标记为已使用。
            *   **做出选择：** 将 `nums[i]` 添加到 `path` 中，并标记 `used[i] = true`。
            *   **递归：** 递归调用 `dfs`，进入下一层，继续填充 `path` 的下一个位置。
        *   **撤销选择 (Unchoose / Backtrack)：** 从递归调用返回后，需要“撤销”之前的选择，恢复到上一个状态：
            *   从 `path` 中移除最后添加的元素。
            *   将 `used[i] = false`，取消标记，使得 `nums[i]` 可以在其他分支中被选用。
    *   **C 语言的内存管理：** 需要手动 `malloc` 所有的结果数组和辅助数组，并且**必须**在 `main` 函数（或调用方）中负责 `free` 所有动态分配的内存。这是 C 语言的“好品味”所在。
    *   **C++ 的内存管理：** 利用 `std::vector` 的 `push_back`/`pop_back` 和自动内存管理，代码会更简洁安全。
    *   **成员变量与参数传递：** 在 C++ `Solution` 类中，`path`, `used`, `ans` 可以作为成员变量。但每次调用 `permute` 时，务必 `clear()` 或 `assign()` 这些成员变量，以确保状态重置。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   所有方案都只读取原始输入 `nums` 数组，不会对其进行修改。
    *   C 语言方案对内存的清晰管理要求调用者承担 `free` 的责任，但这符合 C 语言接口的规范，并不是破坏。

**总结来说，全排列的核心思路就是通过深度优先搜索的“尝试-撤销”机制，系统地探索所有可能的元素组合顺序。每次递归深入一步，就尝试放置一个未使用的元素，直到排列完成。回溯机制确保所有可能性都被探索，并且状态在不同分支之间保持独立。**

### Way Of C

> source code

```c
// 辅助的 DFS 函数，采用“选择-添加”回溯法
// `results`, `count`, `col_sizes` 用于收集结果
// `nums`, `size` 是原始输入数组及其大小
// `used` 数组标记哪些元素已被使用
// `path` 数组存储当前正在构建的排列
// `len` 表示 `path` 中已填充的元素数量
static void dfs_c(int *nums, int size, bool *used, int *path,
                 int len, int ***results_ptr, int *count_ptr, int **col_sizes_ptr)
{
    // 递归终止条件：当前排列已构建完成 (长度达到 size)
    if (len == size) {
        // 1. 分配内存给当前排列
        (*results_ptr)[*count_ptr] = (int*)malloc(size * sizeof(int));
        if ((*results_ptr)[*count_ptr] == NULL) {
            fprintf(stderr, "Memory allocation failed for result permutation\n");
            exit(EXIT_FAILURE); // 内存分配失败是严重问题，直接退出
        }
        // 2. 复制 path 到结果集中
        memcpy((*results_ptr)[*count_ptr], path, size * sizeof(int));
        // 3. 记录当前排列的长度
        (*col_sizes_ptr)[*count_ptr] = size;
        // 4. 增加排列计数
        (*count_ptr)++;
        return;
    }

    // 遍历所有可能的数字，尝试将它们添加到当前排列的 len 位置
    for (int i = 0; i < size; i++) {
        // 如果 nums[i] 尚未被使用
        if (!used[i]) {
            path[len] = nums[i]; // 将 nums[i] 加入当前排列的第 len 个位置
            used[i] = true;       // 标记 nums[i] 已使用
            
            // 递归到下一层，填充排列的第 len + 1 个位置
            dfs_c(nums, size, used, path, len + 1, results_ptr, count_ptr, col_sizes_ptr);
            
            // 回溯：撤销选择，恢复状态
            used[i] = false; // 标记 nums[i] 未使用，以便其他分支可以选用
        }
    }
}

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *returnColumnSizes array must be malloced, assume caller calls free().
 */
int** permute(int* nums, int numsSize, int* returnSize, int** returnColumnSizes)
{
    // 处理 numsSize == 0 的情况：返回一个空的结果集 (或包含一个空排列)
    if (numsSize == 0) {
        *returnSize = 1; // 一个空排列
        *returnColumnSizes = (int*)malloc(sizeof(int));
        if (*returnColumnSizes == NULL) { exit(EXIT_FAILURE); }
        (*returnColumnSizes)[0] = 0;
        int** results = (int**)malloc(sizeof(int*));
        if (results == NULL) { free(*returnColumnSizes); exit(EXIT_FAILURE); }
        results[0] = NULL; // 空排列
        return results;
    }

    // 计算结果集的最大容量：N!
    long long max_perms = 1;
    for (int i = 2; i <= numsSize; ++i) {
        max_perms *= i;
    }
    // 检查溢出，虽然对于 int N! 会很快溢出 long long
    // 但题目通常 N 较小 (<= 10)
    if (max_perms > 10000000) { // 假设一个合理的最大限制，防止分配过大内存
        fprintf(stderr, "Too many permutations to allocate memory for.\n");
        exit(EXIT_FAILURE);
    }
    
    // 动态分配存储结果的二维数组指针
    int** results = (int**)malloc(max_perms * sizeof(int*));
    if (results == NULL) {
        fprintf(stderr, "Memory allocation failed for results array\n");
        exit(EXIT_FAILURE);
    }
    
    // 动态分配存储每个排列长度的数组
    *returnColumnSizes = (int*)malloc(max_perms * sizeof(int));
    if (*returnColumnSizes == NULL) {
        free(results);
        fprintf(stderr, "Memory allocation failed for returnColumnSizes array\n");
        exit(EXIT_FAILURE);
    }
    
    // 辅助数组：用于标记元素是否已使用
    bool* used = (bool*)calloc(numsSize, sizeof(bool)); // calloc 初始化为 false
    if (used == NULL) {
        free(results);
        free(*returnColumnSizes);
        fprintf(stderr, "Memory allocation failed for used array\n");
        exit(EXIT_FAILURE);
    }
    
    // 辅助数组：用于构建当前排列
    int* path = (int*)malloc(numsSize * sizeof(int));
    if (path == NULL) {
        free(results);
        free(*returnColumnSizes);
        free(used);
        fprintf(stderr, "Memory allocation failed for path array\n");
        exit(EXIT_FAILURE);
    }

    *returnSize = 0; // 初始化已找到的排列数量
    
    // 调用 DFS 进行回溯
    dfs_c(nums, numsSize, used, path, 0, &results, returnSize, returnColumnSizes);
    
    // 释放辅助数组
    free(used);
    free(path);
    
    return results; // 返回结果集，调用者负责释放 results 及其子数组，以及 *returnColumnSizes
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    // 外部调用接口
    std::vector<std::vector<int>> permute(std::vector<int>& nums) {
        // 在每次调用时，清空和重置所有成员变量，确保函数调用的独立性。
        ans_.clear();
        path_.clear(); // path_ 在 dfs 中会使用 push_back/pop_back
        used_.assign(nums.size(), false); // 重置 used_ 数组大小并初始化为 false

        // 如果 nums 为空，返回一个包含一个空vector的vector {{}}
        if (nums.empty()) {
            ans_.push_back({});
            return ans_;
        }
        
        // 调用 DFS 开始生成排列
        dfs_cpp(nums);
        
        return ans_;
    }

private:
    // 成员变量用于存储状态，避免频繁的参数传递
    std::vector<std::vector<int>> ans_; // 存储所有找到的排列结果
    std::vector<int> path_;            // 存储当前正在构建的排列
    std::vector<bool> used_;            // 标记 nums 中哪些元素已被使用

    // DFS 辅助函数，不再需要传递 path, used, ans 作为参数，因为它们是成员变量
    void dfs_cpp(const std::vector<int>& nums) { // nums 用 const 引用，只读
        // 递归终止条件：当前排列的长度等于原始数组的长度
        if (path_.size() == nums.size()) {
            ans_.push_back(path_); // 将当前完整排列添加到结果集
            return;
        }

        // 遍历所有可能的数字，尝试将它们添加到当前排列
        for (int i = 0; i < nums.size(); ++i) {
            // 如果 nums[i] 尚未被使用
            if (!used_[i]) {
                used_[i] = true;          // 标记 nums[i] 已使用
                path_.push_back(nums[i]); // 将 nums[i] 添加到当前排列
                
                // 递归：继续填充排列的下一个位置
                dfs_cpp(nums);
                
                // 回溯：撤销选择，恢复状态
                path_.pop_back();         // 从当前排列中移除 nums[i]
                used_[i] = false;         // 标记 nums[i] 未使用，以便其他分支可以选用
            }
        }
    }
};
```

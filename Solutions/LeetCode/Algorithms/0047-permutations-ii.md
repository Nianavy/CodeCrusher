# Question

[permutations-ii](https://leetcode.cn/problems/permutations-ii/)



# Answer

> solution

解决“包含重复数字的全排列”问题的最佳实践是：**首先对输入数组进行排序，然后采用回溯算法，在递归选择过程中加入一个独特的剪枝条件，以避免生成重复的排列。**

1.  **第一步永远是简化数据结构。**
    *   **思想：** 除了回溯必需的 `path` 和 `used` 数组外，我们还需要一个排序后的数组副本。
    *   **实现：**
        *   `sorted_nums` (或 `nums` 的拷贝)：排序后的数组，它是剪枝逻辑能够生效的前提。
        *   `path` (或 `stack`) 数组：存储当前正在构建的排列。
        *   `used` (或 `st`) 数组：布尔类型，标记 `sorted_nums` 中的元素是否已经在 `path` 中被使用。
        *   `results` (或 `ans`) 数组：收集所有完成的唯一排列。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   **空数组输入：** 当 `numsSize` 为 `0` 时，返回一个包含一个空排列的结果集 `{{}}`。
    *   **回溯终止条件：** 当 `path` 的长度达到 `numsSize` 时，将当前 `path` 作为一个唯一的排列添加到 `results` 中。
    *   **核心：预排序与剪枝**：
        *   **预排序 (`qsort`/`std::sort`)：** 这是解决重复数字问题的关键第一步。排序确保了所有相同的数字都会相邻，这样我们才能有效地通过比较相邻元素来进行剪枝。
        *   **剪枝条件 (`i > 0 && nums[i - 1] == nums[i] && !used[i - 1]`)：** 这是最精妙的部分，用于避免生成重复排列。在遍历 `nums` (排序后的) 数组，尝试将 `nums[i]` 添加到 `path` 时：
            *   **`i > 0`：** 确保不是第一个元素，可以安全地访问 `nums[i-1]`。
            *   **`nums[i - 1] == nums[i]`：** 检查当前数字 `nums[i]` 是否与其前一个数字 `nums[i-1]` 相同。如果它们相同，我们就有可能生成重复排列。
            *   **`!used[i - 1]`：** 这是关键！它表示前一个与 `nums[i]` 相同的数字 `nums[i-1]` **尚未被使用**。这意味着我们正处于同一个 DFS 递归层级，并且 `nums[i-1]` 刚刚被跳过（我们没有选择它）。在这种情况下，如果现在选择 `nums[i]`，那么将会产生与选择 `nums[i-1]` 相同的排列分支，导致结果重复。因此，我们跳过 `nums[i]`，只选择 `nums[i-1]` 来代表这一组相同的数字。
            *   **简而言之：** 对于一组重复的数字，在同一层递归中，只有当它**前一个完全相同的数字**被使用了，我们才可以使用当前的这个数字。如果前一个完全相同的数字没有被使用，那么我们应该跳过当前数字。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **DFS 辅助函数：** 定义一个递归的 `dfs` 函数。
    *   **递归过程：**
        *   **基本情况：** `path` 长度达到 `numsSize`，添加 `path` 到 `results`。
        *   **选择：** 遍历 `sorted_nums` 的所有元素 `nums[i]`。
            *   **跳过已使用的元素。**
            *   **应用剪枝条件。**
            *   如果通过检查，则将 `nums[i]` 添加到 `path`，标记 `used[i] = true`，然后递归。
        *   **撤销：** 从递归返回后，`path` 移除元素，`used[i] = false`。
    *   **C 语言的内存管理：** 需要手动 `malloc` 所有的结果数组和辅助数组，并严格检查 `malloc` 返回值，确保调用方 `free` 所有内存。
    *   **C++ 的内存管理：** 利用 `std::vector` 自动管理内存，代码会更简洁安全。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   为了遵守“不修改输入”的原则，应该在函数内部创建 `nums` 的一个副本，并在该副本上进行排序和操作。这会增加 O(N) 空间和 O(N log N) 时间。如果允许修改输入，则可以直接在 `nums` 上操作。

**总结来说，解决“包含重复数字的全排列”问题的核心在于：先将数组排序，然后利用回溯算法的递归结构，并在每个选择点巧妙地使用 `used` 数组和 `nums[i-1] == nums[i] && !used[i-1]` 这样的剪枝逻辑，来精确地排除所有重复的排列，从而只生成唯一的排列。**

### Way Of C

> source code

```c
// 比较函数，用于 qsort
static int compare_ints(const void *a, const void *b)
{
    return *(const int *) a - *(const int *) b;
}

// 辅助的 DFS 函数
// `results_ptr`, `count_ptr`, `col_sizes_ptr` 用于收集结果
// `nums` 是经过排序的副本数组（只读），`size` 是其大小
// `used` 数组标记哪些元素已被使用
// `path` 数组存储当前正在构建的排列
// `len` 表示 `path` 中已填充的元素数量
static void dfs_unique_c(int *nums, int size, bool *used, int *path,
                         int len, int ***results_ptr, int *count_ptr, int **col_sizes_ptr)
{
    // 递归终止条件：当前排列已构建完成 (长度达到 size)
    if (len == size) {
        // 分配内存给当前排列
        (*results_ptr)[*count_ptr] = (int*)malloc(size * sizeof(int));
        if ((*results_ptr)[*count_ptr] == NULL) {
            fprintf(stderr, "Memory allocation failed for result permutation\n");
            exit(EXIT_FAILURE);
        }
        memcpy((*results_ptr)[*count_ptr], path, size * sizeof(int)); // 复制 path
        (*col_sizes_ptr)[*count_ptr] = size; // 记录长度
        (*count_ptr)++;                     // 增加计数
        return;
    }

    // 遍历所有可能的数字，尝试将它们添加到当前排列的 len 位置
    for (int i = 0; i < size; i++) {
        // 如果 nums[i] 尚未被使用
        if (!used[i]) {
            // 剪枝逻辑：避免重复排列
            // 条件1: i > 0 确保 nums[i-1] 存在
            // 条件2: nums[i - 1] == nums[i] 确保当前数字与前一个数字相同
            // 条件3: !used[i - 1] 确保前一个相同的数字在当前递归层级中尚未被使用（即它被跳过了）。
            //   如果这三个条件都满足，说明我们之前已经处理过相同的数字，
            //   现在再选 nums[i] 会产生重复的排列，所以跳过。
            if (i > 0 && nums[i - 1] == nums[i] && !used[i - 1]) {
                continue;
            }
            
            path[len] = nums[i]; // 将 nums[i] 加入当前排列的第 len 个位置
            used[i] = true;      // 标记 nums[i] 已使用
            
            // 递归到下一层
            dfs_unique_c(nums, size, used, path, len + 1, results_ptr, count_ptr, col_sizes_ptr);
            
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
int** permuteUnique(int* nums, int numsSize, int* returnSize, int **returnColumnSizes)
{
    // 1. 处理 numsSize == 0 的特殊情况
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

    // 2. 复制输入数组并排序，以避免修改原始输入
    int* sorted_nums = (int*)malloc(numsSize * sizeof(int));
    if (sorted_nums == NULL) {
        fprintf(stderr, "Memory allocation failed for sorted_nums\n");
        exit(EXIT_FAILURE);
    }
    memcpy(sorted_nums, nums, numsSize * sizeof(int));
    qsort(sorted_nums, numsSize, sizeof(int), compare_ints);

    // 3. 计算结果集的最大容量：N!
    long long max_perms = 1;
    for (int i = 2; i <= numsSize; ++i) {
        max_perms *= i;
    }
    // 对于 N 较小时，max_perms 可能远大于实际唯一排列数，但这是上限。
    // 如果 N 很大导致 max_perms 溢出或内存过大，可能需要更复杂的策略。
    
    int** results = (int**)malloc(max_perms * sizeof(int*));
    if (results == NULL) {
        free(sorted_nums);
        fprintf(stderr, "Memory allocation failed for results array\n");
        exit(EXIT_FAILURE);
    }
    
    *returnColumnSizes = (int*)malloc(max_perms * sizeof(int));
    if (*returnColumnSizes == NULL) {
        free(sorted_nums);
        free(results);
        fprintf(stderr, "Memory allocation failed for returnColumnSizes array\n");
        exit(EXIT_FAILURE);
    }
    
    bool* used = (bool*)calloc(numsSize, sizeof(bool)); // calloc 初始化为 false
    if (used == NULL) {
        free(sorted_nums);
        free(results);
        free(*returnColumnSizes);
        fprintf(stderr, "Memory allocation failed for used array\n");
        exit(EXIT_FAILURE);
    }
    
    int* path = (int*)malloc(numsSize * sizeof(int));
    if (path == NULL) {
        free(sorted_nums);
        free(results);
        free(*returnColumnSizes);
        free(used);
        fprintf(stderr, "Memory allocation failed for path array\n");
        exit(EXIT_FAILURE);
    }

    *returnSize = 0; // 初始化已找到的排列数量
    
    // 4. 调用 DFS 进行回溯
    dfs_unique_c(sorted_nums, numsSize, used, path, 0, &results, returnSize, returnColumnSizes);
    
    // 5. 释放辅助数组和排序副本
    free(used);
    free(path);
    free(sorted_nums); // 释放 sorted_nums 副本

    return results; // 调用者负责释放 results 及其子数组，以及 *returnColumnSizes
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::vector<std::vector<int>> permuteUnique(std::vector<int>& nums) {
        // 1. 每次调用时，清空和重置所有成员变量，确保函数调用的独立性。
        ans_.clear();
        path_.clear(); // path_ 会被 resize，但确保逻辑清晰
        path_.resize(nums.size()); // 预分配空间给 path_，因为 dfs_cpp 中直接赋值 path_[u]
        used_.assign(nums.size(), false); // 重置 used_ 数组大小并初始化为 false

        // 2. 如果 nums 为空，返回一个包含一个空vector的vector {{}}
        if (nums.empty()) {
            ans_.push_back({});
            return ans_;
        }

        // 3. 复制输入数组并排序，以避免修改原始输入 nums。
        // 这是为了遵守“不修改输入”的原则，并为剪枝做准备。
        std::vector<int> sorted_nums = nums; 
        std::sort(sorted_nums.begin(), sorted_nums.end()); 
        
        // 4. 调用 DFS 开始生成排列
        dfs_unique_cpp(sorted_nums, 0); // 将排序后的副本传给 DFS
        
        return ans_;
    }

private:
    // 成员变量用于存储状态，避免频繁的参数传递
    std::vector<std::vector<int>> ans_; // 存储所有找到的唯一排列结果
    std::vector<int> path_;            // 存储当前正在构建的排列
    std::vector<bool> used_;            // 标记 sorted_nums 中哪些元素已被使用

    // u 表示当前正在填充排列的第 u 个位置
    void dfs_unique_cpp(const std::vector<int>& nums, int u) { // nums 传入 const 引用
        // 递归终止条件：当前排列的长度等于原始数组的长度
        if (u == nums.size()) {
            ans_.push_back(path_); // 将当前完整排列添加到结果集
            return;
        }

        // 遍历所有可能的数字，尝试将它们添加到当前排列的第 u 个位置
        for (int i = 0; i < nums.size(); ++i) {
            // 1. 如果 nums[i] 已经被使用过，则跳过
            if (used_[i]) {
                continue;
            }
            
            // 2. 剪枝逻辑：避免重复排列
            // 只有当满足以下所有条件时，才跳过当前数字 nums[i]：
            //   a. `i > 0`：确保 `nums[i-1]` 存在。
            //   b. `nums[i - 1] == nums[i]`：当前数字与前一个数字相同。
            //   c. `!used_[i - 1]`：前一个相同的数字在当前 DFS 层级中尚未被使用。
            //      这表示 `nums[i-1]` 刚刚被跳过了。如果现在选择 `nums[i]`，
            //      就会生成与之前选择 `nums[i-1]` 相同的排列路径，所以跳过 `nums[i]`。
            if (i > 0 && nums[i - 1] == nums[i] && !used_[i - 1]) {
                continue;
            }
            
            used_[i] = true;          // 标记 nums[i] 已使用
            path_[u] = nums[i];       // 将 nums[i] 放到当前排列的第 u 个位置
            
            // 递归：继续填充排列的下一个位置
            dfs_unique_cpp(nums, u + 1);
            
            // 回溯：撤销选择，恢复状态
            used_[i] = false;         // 标记 nums[i] 未使用，以便其他分支可以选用
            // path_[u] 不需要重置，因为下一轮循环会覆盖它
        }
    }
};
```

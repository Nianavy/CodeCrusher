# Question

[path-sum-ii](https://leetcode.cn/problems/path-sum-ii/)



# Answer

> solution

## 查找所有路径和

### 1. 算法核心：DFS 回溯

与仅检查路径是否存在（布尔返回）不同，找到所有路径需要我们在 DFS 过程中**动态记录**当前走过的节点，并在回溯时**撤销**这些记录。

**核心机制：**

1.  **路径栈 (Stack)**：使用一个数组或 `std::vector` 作为栈，记录当前从根节点到递归位置的路径。
2.  **推进 (Push)**：在进入子节点的递归调用前，将当前节点值推入路径栈。同时，将目标和 `targetSum` 减去当前节点值，并传递新的剩余和。
3.  **成功检查**：只有当到达**叶子节点**时，才检查 `remaining_sum` 是否为 0。如果匹配，将当前路径栈复制到结果集中。
4.  **回溯 (Pop)**：在递归调用返回后（无论成功或失败），必须将当前节点从路径栈中弹出。这确保了当 DFS 切换到同层的兄弟节点时，路径栈是正确的。

### 2. C++ 解决思路：高抽象与简洁性

C++ 版本的思路是利用标准模板库 (STL) 来抽象化所有内存和状态管理，使得回溯逻辑异常简洁。

*   **路径管理**：使用 `std::vector<int>` 作为路径栈。`push_back()` 和 `pop_back()` 自动处理内存的增长和收缩。
*   **结果收集**：使用 `std::vector<std::vector<int>>` 自动管理结果集的容量和内存。
*   **状态传递**：将路径栈 (`current_path`) 和结果集 (`all_paths`) 作为**引用**传递给递归函数，避免了不必要的复制开销。

**最终结果**：C++ 代码专注于算法逻辑，避免了任何手动内存管理。

### 3. C 解决思路：内存契约与动态风险

C 版本的思路是必须手动实现所有动态行为，并严格遵守内存契约。这带来了巨大的复杂性和风险。

*   **路径栈**：由于 C 不支持可变大小的栈，我们必须在堆上 `malloc` 一个固定大小的数组 (`path_stack`) 来模拟栈。如果树深度超过这个硬编码限制，就会导致越界写入。
*   **结果容量**：必须手动实现**动态扩容**逻辑 (`realloc`)。为了避免结果数组溢出，`pathSum` 必须包含 `resize_results` 辅助函数，在每次找到新路径时检查并扩大 `results` 和 `returnColumnSizes` 的容量。
*   **内存泄漏**：C 代码必须显式释放所有 `malloc` 的内存：
    1. 临时工作栈 (`path_stack`)。
    2. 结果集中的每一条路径（内层数组）。
    3. 结果集的行指针数组 (`results`) 和列大小数组 (`sizes`)。

**最终结论**：回溯逻辑在两种语言中是相同的，但 C++ 以简洁优雅地完成了 $O(N)$ 任务，而 C 则因为需要处理动态扩容和多重内存释放，代码复杂度增加了数倍。在涉及动态结果集合的问题中，C 的维护成本极高。

### Way Of C

> source code

```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */
/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
/**
 * 定义一个合理的树最大深度，避免栈数组溢出。
 * 在实际应用中，如果树深度未知，需要将 stack 也改为动态分配和 realloc。
 */
#define MAX_DEPTH 1000 
#define INITIAL_CAPACITY 32

// 辅助结构体，用于管理动态结果数组的容量
typedef struct {
    int **results;
    int *sizes;
    int count;
    int capacity;
} ResultManager;

// 辅助函数：尝试对结果数组进行扩容
static void resize_results(ResultManager *mgr) {
    if (mgr->count == mgr->capacity) {
        // 扩容到两倍
        mgr->capacity *= 2;
        
        // 扩容结果指针数组
        mgr->results = realloc(mgr->results, mgr->capacity * sizeof(int *));
        if (!mgr->results) exit(EXIT_FAILURE);

        // 扩容列大小数组
        mgr->sizes = realloc(mgr->sizes, mgr->capacity * sizeof(int));
        if (!mgr->sizes) exit(EXIT_FAILURE);
    }
}

// 核心回溯函数
static void dfs_backtrack(struct TreeNode *root, int remaining_sum, 
                          int *path_stack, int current_len, 
                          ResultManager *mgr)
{
    if (root == NULL) {
        return;
    }

    // 1. 推进状态：将当前节点值加入路径栈
    // 检查路径栈是否溢出
    if (current_len >= MAX_DEPTH) {
        // 通常在 C 语言中，这代表输入数据有问题
        fprintf(stderr, "Error: Max tree depth exceeded.\n");
        return; 
    }
    path_stack[current_len] = root->val;
    int new_remaining_sum = remaining_sum - root->val;
    int new_len = current_len + 1;

    // 2. 检查停止条件：到达叶子节点
    if (root->left == NULL && root->right == NULL) {
        if (new_remaining_sum == 0) {
            // 找到有效路径
            
            // 确保结果数组有空间
            resize_results(mgr);

            // 复制当前路径到结果集
            int path_size = new_len;
            mgr->results[mgr->count] = malloc(path_size * sizeof(int));
            if (!mgr->results[mgr->count]) exit(EXIT_FAILURE);
            
            memcpy(mgr->results[mgr->count], path_stack, path_size * sizeof(int));
            mgr->sizes[mgr->count] = path_size;
            
            mgr->count++;
        }
        // 注意：无需 path_stack[len - 1] = 0; 或 pop_back()，因为我们是传值 len + 1
        // 回溯自然发生 (current_len 在函数返回后恢复)
        return;
    } 

    // 3. 继续探索
    dfs_backtrack(root->left, new_remaining_sum, path_stack, new_len, mgr);
    dfs_backtrack(root->right, new_remaining_sum, path_stack, new_len, mgr);
    
    // 4. 回溯：无需 pop_back，因为 path_stack 是通过传值 len 实现的逻辑回溯。
}

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *returnColumnSizes array must be malloced, assume caller calls free().
 */
int** pathSum(struct TreeNode* root, int targetSum, int* returnSize, int** returnColumnSizes) {
    if (root == NULL) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    // 初始化动态结果管理器
    ResultManager mgr;
    mgr.count = 0;
    mgr.capacity = INITIAL_CAPACITY;
    mgr.results = malloc(mgr.capacity * sizeof(int *));
    mgr.sizes = malloc(mgr.capacity * sizeof(int));
    if (!mgr.results || !mgr.sizes) exit(EXIT_FAILURE);
    
    // 初始化路径栈 (必须在堆上分配，以支持深层递归)
    int *path_stack = malloc(MAX_DEPTH * sizeof(int));
    if (!path_stack) exit(EXIT_FAILURE);

    // 启动回溯搜索
    dfs_backtrack(root, targetSum, path_stack, 0, &mgr);
    
    // 释放 DFS 路径栈，这是在堆上分配的临时工作内存
    free(path_stack);

    // 整理最终返回给调用者的指针
    *returnSize = mgr.count;
    *returnColumnSizes = mgr.sizes;
    return mgr.results;
}

// 必须：提供内存清理函数供调用者使用 (清理 pathSum 的返回值)
void freePathSumResults(int** results, int* sizes, int count) {
    if (results == NULL) return;
    for (int i = 0; i < count; i++) {
        free(results[i]); // 清理内层数组
    }
    free(results); // 清理外层指针数组
    free(sizes);   // 清理列大小数组
}
```

---

### Way Of C++

> source code

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    // 回溯辅助函数：path是当前路径栈，res是最终结果集合
    void dfs_backtrack(TreeNode* root, 
                       int remaining_sum, 
                       std::vector<int>& current_path, 
                       std::vector<std::vector<int>>& all_paths) 
    {
        if (root == nullptr) {
            return;
        }

        // 1. 推进状态：将当前节点值加入路径
        current_path.push_back(root->val);
        int new_remaining_sum = remaining_sum - root->val;

        // 2. 检查停止条件：到达叶子节点
        if (root->left == nullptr && root->right == nullptr) {
            if (new_remaining_sum == 0) {
                // 找到有效路径：将当前路径复制到结果集合中
                all_paths.push_back(current_path);
            }
        } 
        // 3. 继续探索
        else {
            if (root->left) {
                dfs_backtrack(root->left, new_remaining_sum, current_path, all_paths);
            }
            if (root->right) {
                dfs_backtrack(root->right, new_remaining_sum, current_path, all_paths);
            }
        }

        // 4. 回溯：退出递归前，从路径中移除当前节点
        current_path.pop_back();
    }

public:
    std::vector<std::vector<int>> pathSum(TreeNode* root, int targetSum) {
        std::vector<std::vector<int>> all_paths;
        std::vector<int> current_path; // 局部回溯栈
        
        dfs_backtrack(root, targetSum, current_path, all_paths);
        return all_paths;
    }
};
```

# Question

[binary-tree-level-order-traversal](https://leetcode.cn/problems/binary-tree-level-order-traversal/)



# Answer

> solution

### STL驱动的按层 BFS(C++)

解决该问题的思路是利用队列的即时大小作为层级边界：

1.  **初始化**：
    *   使用 `std::queue<TreeNode*>` 存储待访问的节点。
    *   根节点入队。
2.  **BFS 循环**：
    *   只要队列不为空，循环继续。
    *   在循环开始时，**锁定当前队列的大小 `size = q.size()`**。这个 `size` 就是当前层的节点总数。
3.  **层级处理**：
    *   内层循环精确地执行 `size` 次。
    *   每次循环中，一个节点出队，将其值添加到当前的 `level` 向量中。
    *   节点的非空子节点（`left`, `right`）被推入队列。这些新入队的节点将构成下一层。
4.  **结果收集**：当前层的 `level` 向量处理完毕后，将其添加到最终结果 `res` 中。

### 扁平化 BFS(C)

解决该问题的思路是使用一个**动态增长的数组**作为队列，并进行严格的内存管理：

1.  **数据结构**：使用一个 `struct TreeNode**` 数组作为队列，通过 `q_head` 和 `q_tail` 指针实现入队/出队操作。
2.  **动态管理**：由于不知道树的深度和宽度，队列和结果集（`results` 和 `col_sizes`）必须通过**几何增长**（容量 $\times 2$）的方式使用 `realloc` 进行动态扩容。
3.  **层级锁定**：在 `while (q_head < q_tail)` 循环内，定义 `int size = q_tail - q_head;` 来精确锁定当前层应处理的节点数。
4.  **内存管理铁律**：分配了三个主要堆块（`queue`, `results`, `col_sizes`），函数结束时必须**全部释放** `queue`。至于 `results` 和 `col_sizes`，C 规范要求调用者释放。

### Way Of C

> source code

```c
// 初始容量，会根据需要 realloc
#define INITIAL_CAPACITY 32

/**
 * 返回一个整数指针的数组，其中每个内部数组代表一层节点的值。
 * 必须释放所有 malloc 的内存。
 */
int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes) {
    if (root == NULL) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    // --- 队列初始化 (使用动态数组作为队列) ---
    struct TreeNode **queue = (struct TreeNode**)malloc(INITIAL_CAPACITY * sizeof(struct TreeNode*));
    if (!queue) { *returnSize = 0; return NULL; }
    
    int q_head = 0;
    int q_tail = 0;
    int q_capacity = INITIAL_CAPACITY;
    
    // 入队根节点
    queue[q_tail++] = root;

    // --- 结果集初始化 ---
    int max_levels = INITIAL_CAPACITY;
    int **results = (int**)malloc(max_levels * sizeof(int*));
    int *col_sizes = (int*)malloc(max_levels * sizeof(int));
    if (!results || !col_sizes) { free(queue); *returnSize = 0; return NULL; }

    int current_level = 0;

    // BFS 主循环：当队列不为空时继续 (q_head < q_tail)
    while (q_head < q_tail) {
        
        // 1. 结果数组扩容检查
        if (current_level >= max_levels) {
            max_levels *= 2;
            results = (int**)realloc(results, max_levels * sizeof(int*));
            col_sizes = (int*)realloc(col_sizes, max_levels * sizeof(int));
            if (!results || !col_sizes) { /* 内存分配失败处理 */ free(queue); /* 还需要释放已经分配的 results[i] */ return NULL; }
        }

        int size = q_tail - q_head; // 锁定当前层的大小
        col_sizes[current_level] = size;
        
        // 2. 分配当前层结果数组
        results[current_level] = (int*)malloc(size * sizeof(int));
        if (!results[current_level]) { free(queue); /* 内存分配失败处理 */ return NULL; }
        
        // 3. 遍历当前层并入队下一层
        for (int i = 0; i < size; i++) {
            struct TreeNode *node = queue[q_head++]; // 出队

            // 记录值
            results[current_level][i] = node->val;
            
            // 入队子节点：需要检查队列容量是否足够
            
            // 检查队列扩容 (只需检查下一层可能的最大节点数)
            // 这里我们只需要确保 q_tail + 2 < q_capacity
            if (q_tail + 2 >= q_capacity) {
                q_capacity *= 2;
                queue = (struct TreeNode**)realloc(queue, q_capacity * sizeof(struct TreeNode*));
                if (!queue) { /* 内存分配失败处理 */ return NULL; }
            }

            if (node->left != NULL) {
                queue[q_tail++] = node->left;
            }
            if (node->right != NULL) {
                queue[q_tail++] = node->right;
            }
        }
        current_level++;
    }

    // --- 清理资源和返回 ---
    *returnSize = current_level;
    *returnColumnSizes = col_sizes;
    
    // 释放队列占用的内存
    free(queue);
    
    return results;
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
public:
    std::vector<std::vector<int>> levelOrder(TreeNode* root) {
        std::vector<std::vector<int>> res;
        if (root == nullptr) {
            return res;
        }

        std::queue<TreeNode*> q;
        q.push(root);

        // 标准 BFS 循环
        while (!q.empty()) {
            std::vector<int> level;
            int size = q.size(); // 锁定当前层的节点数

            // 循环 size 次，处理当前层
            for (int i = 0; i < size; i++) {
                TreeNode *node = q.front();
                q.pop();
                
                level.push_back(node->val);
                
                if (node->left != nullptr) {
                    q.push(node->left);
                }
                if (node->right != nullptr) {
                    q.push(node->right);
                }
            }
            res.push_back(level);
        }

        return res;
    }
};
```

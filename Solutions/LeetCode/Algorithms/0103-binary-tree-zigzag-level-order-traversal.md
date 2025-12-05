# Question

[binary-tree-zigzag-level-order-traversal](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)



# Answer

> solution

### 按层锁定与方向写入

解决该问题的思路是，在标准 BFS 框架内，精确锁定当前层并决定写入索引：

1.  **BFS 基础**：使用队列，并在外层循环中，通过 `size = q.size()` 锁定当前层节点数。
2.  **方向判断**：使用 `current_level % 2` 判断当前层是否需要逆序。
3.  **C++ 实现**：
    *   将所有节点值按标准 L $\rightarrow$ R 顺序收集到 `level` 向量。
    *   如果需要逆序，调用 `std::reverse(level.begin(), level.end())`。
4.  **C 语言实现**：
    *   在内层循环中，计算写入索引 `write_index`：
        *   如果正向：`write_index = i`。
        *   如果逆向：`write_index = size - 1 - i`。
    *   将节点值直接写入 `results[current_level][write_index]`。

无论是 C 还是 C++，都必须保证队列和结果集是**动态管理**的，以满足实用主义的内存要求。

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
// 初始容量，为简洁起见，使用 realloc 动态管理
#define INITIAL_CAPACITY 32

// 返回一个整数指针的数组，其中每个内部数组代表一层节点的值 (锯齿形)。
// 使用动态数组模拟队列和结果集。
int** zigzagLevelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes) {
    if (root == NULL) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    // --- 队列初始化 ---
    struct TreeNode **queue = (struct TreeNode**)malloc(INITIAL_CAPACITY * sizeof(struct TreeNode*));
    int q_head = 0;
    int q_tail = 0;
    int q_capacity = INITIAL_CAPACITY;
    queue[q_tail++] = root;

    // --- 结果集初始化 ---
    int max_levels = INITIAL_CAPACITY;
    int **results = (int**)malloc(max_levels * sizeof(int*));
    int *col_sizes = (int*)malloc(max_levels * sizeof(int));
    if (!results || !col_sizes) { free(queue); *returnSize = 0; return NULL; }

    int current_level = 0; // 0-based 索引: 0, 1, 2, ...

    while (q_head < q_tail) {
        
        // 结果数组扩容检查
        if (current_level >= max_levels) {
            max_levels *= 2;
            results = (int**)realloc(results, max_levels * sizeof(int*));
            col_sizes = (int*)realloc(col_sizes, max_levels * sizeof(int));
            if (!results || !col_sizes) { /* 内存失败处理 */ free(queue); return NULL; }
        }

        int size = q_tail - q_head; // 锁定当前层大小
        col_sizes[current_level] = size;
        
        // 分配当前层结果数组
        results[current_level] = (int*)malloc(size * sizeof(int));
        if (!results[current_level]) { free(queue); return NULL; }
        
        // 检查方向: 0, 2, 4... (偶数层，从左到右); 1, 3, 5... (奇数层，从右到左)
        bool reverse_write = (current_level % 2 != 0); 
        
        // 遍历当前层并入队下一层
        for (int i = 0; i < size; i++) {
            struct TreeNode *node = queue[q_head++]; // 出队

            // --- 核心：根据方向决定写入索引 ---
            int write_index = reverse_write ? (size - 1 - i) : i;
            results[current_level][write_index] = node->val;
            
            // 检查队列扩容
            if (q_tail + 2 >= q_capacity) {
                q_capacity *= 2;
                queue = (struct TreeNode**)realloc(queue, q_capacity * sizeof(struct TreeNode*));
                if (!queue) { /* 内存失败处理 */ return NULL; }
            }

            // 入队子节点 (始终是 L -> R 的标准顺序，只在结果中调整)
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
    std::vector<std::vector<int>> zigzagLevelOrder(TreeNode* root) {
        std::vector<std::vector<int>> res;
        if (root == nullptr) {
            return res;
        }

        std::queue<TreeNode*> q;
        q.push(root);
        bool reverse_order = false; // 0层(根)是 false (左到右)

        while (!q.empty()) {
            std::vector<int> level;
            int size = q.size(); 

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
            
            // 奇数层 (1, 3, 5...) 需要反转
            if (reverse_order) {
                std::reverse(level.begin(), level.end());
            }
            
            res.push_back(level);
            reverse_order = !reverse_order; // 切换下一层的方向
        }

        return res;
    }
};
```

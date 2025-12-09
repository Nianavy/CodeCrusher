# Question

[binary-tree-level-order-traversal-ii](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/)



# Answer

> solution

## 从底到上的层序遍历

### 1. 算法核心：基于队列的广度优先搜索 (BFS)

这是唯一正确的思路。层序遍历天然要求我们先访问完一层的所有节点，才能进入下一层，这正是队列（先进先出, FIFO）的特性所匹配的。

**核心步骤：**

1.  **初始化**：创建一个队列，将根节点推入。
2.  **迭代**：只要队列非空，就重复以下步骤。
3.  **精确分层**：在每次外层循环开始时，记录队列当前的 `size`。这个 `size` **就是当前层级的节点总数**。
4.  **处理层级**：在一个内层循环中，精确地弹出 `size` 个节点。将它们的值收集起来作为当前层级的结果。
5.  **推进下一层**：在弹出节点时，检查其左右子节点，如果非空，将它们推入队列，为下一次外层循环做准备。
6.  **结果反转**：将收集到的所有层级结果（顺序是从上到下）进行整体反转，以满足“从底到上”的要求。

### 2. C++ 解决思路：工具箱优势

C++ 的思路是最大化利用标准库，以保证代码的简洁性、安全性和效率。

*   **容器**：使用 `std::queue` 处理 BFS 逻辑，使用 `std::vector<std::vector<int>>` 动态存储结果。
*   **内存管理**：零手动内存操作。所有的内存分配和回收都由 `std::vector` 自动处理，消除了所有 `malloc`/`free` 带来的风险。
*   **简洁性**：使用 `std::reverse` 宏一步完成最终的结果倒序。这是典型的 C++ 好品味。

### 3. C 解决思路：手动构建和内存契约

C 语言的思路是必须手动实现所有抽象，同时严格遵守内存分配和释放的契约。

*   **核心挑战**：在 C 中，没有标准的队列或动态数组。必须手动使用结构体和数组来模拟队列（`Queue` 结构）。
*   **内存契约**：由于需要返回 `int**` 和 `int*`，所有这些返回的指针及其指向的内存都必须通过 `malloc` 创建。
    *   **外部数组**：必须为 `results` (行指针数组) 和 `returnColumnSizes` (列大小数组) 分配内存。由于无法预知树的深度，通常需要硬编码一个安全上限 (`MAX_DEPTH`) 或使用 `realloc`（但 `realloc` 会大幅增加复杂性和风险）。
    *   **内部数组**：每次处理一层时，必须精确 `malloc(level_len * sizeof(int))` 来存储该层的数据。
*   **反转**：手动编写循环来交换 `res_list` 和 `col_sizes_list` 中的指针和数值，以完成从底到上的要求。

### 总结

对于层序遍历这种需要动态调整队列和结果数组的操作，**C++ 版本的简洁性和安全性远超 C 版本。** C 版本通过手动实现 BFS 保证了算法的正确性，但其实现复杂性全部来自手动内存管理和对容器的模拟，这是 C 语言实用主义的痛苦代价。

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
// --- 手动实现的队列结构和操作 ---
#define MAX_NODES 10000 // 假设一个合理的上限来避免动态 realloc 队列
#define MAX_DEPTH 1000  // 假设树的最大深度

struct Queue {
    struct TreeNode* nodes[MAX_NODES];
    int front;
    int rear;
};

static void queue_push(struct Queue *q, struct TreeNode *node) {
    if (q->rear < MAX_NODES) q->nodes[q->rear++] = node;
}
static struct TreeNode* queue_pop(struct Queue *q) {
    if (q->front < q->rear) return q->nodes[q->front++];
    return NULL;
}
static int queue_size(const struct Queue *q) {
    return q->rear - q->front;
}
// --- 队列结束 ---


/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** levelOrderBottom(struct TreeNode* root, int* returnSize, int** returnColumnSizes) {
    if (root == NULL) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    struct Queue q = { .front = 0, .rear = 0 };
    queue_push(&q, root);
    
    // 动态分配结果数组（外层指针）和列大小数组
    int **res_list = malloc(MAX_DEPTH * sizeof(int*));
    int *col_sizes_list = malloc(MAX_DEPTH * sizeof(int));
    int level_count = 0;

    // BFS 迭代
    while (queue_size(&q) > 0) {
        if (level_count >= MAX_DEPTH) {
            // 超过预设最大深度，返回错误或处理，这里简化为终止
            break; 
        }

        int level_len = queue_size(&q);
        
        // 分配当前层级数据的内存
        int *current_level = malloc(level_len * sizeof(int));
        col_sizes_list[level_count] = level_len;
        
        for (int i = 0; i < level_len; i++) {
            struct TreeNode *node = queue_pop(&q);
            current_level[i] = node->val;

            if (node->left) queue_push(&q, node->left);
            if (node->right) queue_push(&q, node->right);
        }
        
        res_list[level_count] = current_level;
        level_count++;
    }

    *returnSize = level_count;
    
    // 反转结果数组 (从底到上)
    int i, j;
    for (i = 0, j = *returnSize - 1; i < j; i++, j--) {
        // 交换行指针
        int *ptmp = res_list[i];
        res_list[i] = res_list[j];
        res_list[j] = ptmp;
        // 交换列大小
        int tmp = col_sizes_list[i];
        col_sizes_list[i] = col_sizes_list[j];
        col_sizes_list[j] = tmp;
    }

    *returnColumnSizes = col_sizes_list;
    // 注意：如果实际深度远小于 MAX_DEPTH，res_list 仍分配了 MAX_DEPTH 空间，
    // 但只有 *returnSize 个指针是有效的。调用者应该只释放 *returnSize 个有效行。
    return res_list;
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
    std::vector<std::vector<int>> levelOrderBottom(TreeNode* root) {
        std::vector<std::vector<int>> res;
        if (root == nullptr) {
            return res;
        }

        std::queue<TreeNode *> q;
        q.push(root);
        
        while (!q.empty()) {
            // 精确计算当前层级的节点数
            int size = q.size();
            std::vector<int> level;
            
            for (int i = 0; i < size; i++) {
                TreeNode *node = q.front();
                q.pop();
                
                level.push_back(node->val);
                
                // 推进下一层级的节点
                if (node->left != nullptr) {
                    q.push(node->left);
                }
                if (node->right != nullptr) {
                    q.push(node->right);
                }
            }
            res.push_back(level);
        }

        // 倒序结果，满足从底到上
        std::reverse(res.begin(), res.end());
        return res;
    }
};
```

# Question

[binary-tree-right-side-view](https://leetcode.cn/problems/binary-tree-right-side-view/)



# Answer

> solution

### **解决思路**

**共同算法**：**层级遍历（BFS）**。每处理完一层，将最后一个节点的值记录下来。

**C 语言实现**：
*   **队列**：使用**数组模拟**，分配固定大容量，避免 `malloc` 混乱。
*   **遍历**：`while` 处理层，`for` 处理层内节点。层内循环更新 `last_node`，循环结束后记录该节点值。
*   **内存**：动态分配结果数组；临时队列数组在栈上分配或固定大小 `malloc`，**必须释放**。

**C++ 实现**：
*   **队列**：使用 `std::queue<TreeNode*>`。
*   **遍历**：用 `q.size()` 获取当前层大小，标准 `for` 循环处理。每层循环结束记录值。
*   **内存**：`std::vector<int>` 自动管理，无需手动 `free`。

---

### **歧义处理**

*   **树的大小未知**：C 代码中，队列使用固定容量（如 2000 节点），可覆盖绝大多数题目场景。超大规模树需要更复杂的动态数组链，但降低了简洁性。
*   **性能**：两次遍历（计数 + 遍历）可精确分配内存，但 BFS 本身只需一次。上述方案是标准解法。


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
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* rightSideView(struct TreeNode* root, int* returnSize) {
    *returnSize = 0;
    if (!root) return NULL;
    // 静态分配队列缓冲区和结果缓冲区（可重入性差，但简单）
    // 为了保护，我们动态分配，但大小固定为 2000（覆盖极端情况）
    // *在生产代码中，应使用 realloc 链或两次遍历确定确切大小。*
    
    struct TreeNode** queue = malloc(2000 * sizeof(struct TreeNode*));
    int *result = malloc(500 * sizeof(int));
    if (!queue || !result) {
        free(queue); free(result);
        return NULL;
    }
    
    int head = 0, tail = 0;
    queue[tail++] = root;
    
    // *关键：BFS + 记录每层最后一个节点*
    while (head < tail) {
        int layer_size = tail - head; // 当前层在队列中的数量
        
        // 遍历当前层的节点
        struct TreeNode *last_node = NULL;
        for (int i = 0; i < layer_size; i++) {
            struct TreeNode *node = queue[head++];
            last_node = node; // 每次循环覆盖，最后剩下的就是第 len 个
            
            if (node->left) queue[tail++] = node->left;
            if (node->right) queue[tail++] = node->right;
        }
        
        // 记录下该层的最右侧节点（正好是 last_node）
        result[(*returnSize)++] = last_node->val;
    }
    
    free(queue); // 临时缓冲区，释放
    return result; // 结果，调用者释放
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
    vector<int> rightSideView(TreeNode* root) {
        vector<int> result;
        if (!root) return result;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int levelSize = q.size();
            // 遍历当前层
            for (int i = 0; i < levelSize; ++i) {
                TreeNode* node = q.front();
                q.pop();
                
                // 如果是该层的最后一个节点，记录值
                if (i == levelSize - 1) {
                    result.push_back(node->val);
                }
                
                // 入队子节点，注意顺序：
                // 先左后右，队列先左后右，遍历时右节点会被记录
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }
        return result;
    }
};
```

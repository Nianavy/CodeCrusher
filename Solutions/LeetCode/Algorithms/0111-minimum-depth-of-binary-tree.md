# Question

[minimum-depth-of-binary-tree](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)



# Answer

> solution

## 计算最小深度

### 1. 核心陷阱与定义

最小深度（Min Depth）的定义是到**最近的叶子节点**的路径长度。这里的关键陷阱在于：一个节点如果只有左子树或只有右子树，它不是叶子节点。因此，如果一条路径到达空节点，我们不能将这条路径视为有效路径并将其深度计为 1。

例如，对于只有左子节点 L 的根节点 R，我们不能比较 (L 的深度) 和 1，我们必须沿着 L 的路径继续走。

### 2. 方案选择：实用主义的裁决

在这个问题中，虽然 $O(N)$ 的递归 DFS 可以解决，但**迭代的 BFS (广度优先搜索)** 是更符合最小深度定义的解决方案，也是性能最优的选择。

**BFS 的优势**：

*   **最短路径**：BFS 是逐层搜索的，天然保证了找到的第一个叶子节点就是离根最近的节点。
*   **即时停止**：一旦发现队列中弹出的节点是一个叶子节点（即左右子节点都为空），我们可以立即停止搜索并返回当前的深度。这避免了递归 DFS 必须探索树的所有分支才能确定最小值的开销。

### 3. 迭代 BFS 解决思路（最优方案）

1.  **分层搜索**：使用一个队列来存储节点，并在外层循环中记录当前的深度 (`depth`)。
2.  **边界检查**：在处理每一层时，记录当前队列的大小 (`level_size`)，确保我们只处理当前层级的节点。
3.  **叶子判断与返回**：对于弹出的每一个节点，执行检查：如果 `node->left == NULL && node->right == NULL`，则该节点是叶子节点，当前 `depth` 就是最小深度，立即返回。
4.  **推进**：否则，将有效的左右子节点推入队列，并进入下一层 (`depth++`)。

### 4. 递归 DFS 解决思路（边界复杂的方案）

如果必须使用递归，则必须通过复杂的边界分析来避免空路径的干扰：

1.  **计算**：递归计算左右子树的最小深度 (`left_depth`, `right_depth`)。
2.  **单边判断**：如果 `left_depth` 为 0，说明左侧是空路径，我们必须返回 `right_depth + 1`。如果 `right_depth` 为 0，则返回 `left_depth + 1`。
3.  **双边判断**：只有当 `left_depth` 和 `right_depth` 都不为 0 时，我们才能安全地取两者的最小值，并加 1。

**总结**：虽然递归方案（如版本 1）通过多个 `if` 分支处理了所有情况，但**迭代 BFS** 是更直接、更高效的 $O(N)$ 解决方案，因为它完美匹配了最小深度（最短路径）的定义。

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
int minDepth(struct TreeNode* root)
{
    if (root == NULL) {
        return 0;
    }

    // 递归计算左右子树的最小深度
    int left_depth = minDepth(root->left);
    int right_depth = minDepth(root->right);

    // 核心边界分析：
    // 1. 如果左右子树都为空 (即都是0)，则返回 1 (叶子节点)
    if (left_depth == 0 && right_depth == 0) {
        return 1;
    }
    
    // 2. 如果只有一边为空 (例如 left_depth == 0)，则最小深度必须是右侧的深度
    //    不能取 0 + 1 = 1，因为 1 不是叶子节点。
    if (left_depth == 0) {
        return right_depth + 1;
    }
    if (right_depth == 0) {
        return left_depth + 1;
    }

    // 3. 左右子树都存在，返回两者中的最小值
    // 注意：在这里，我们不需要 + 1，因为 left_depth/right_depth 已经包含了 + 1 
    // 上述逻辑简化后：
    return (left_depth < right_depth ? left_depth : right_depth) + 1; 
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
    int minDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }

        std::queue<TreeNode*> q;
        q.push(root);
        int depth = 1;

        while (!q.empty()) {
            int level_size = q.size();
            
            for (int i = 0; i < level_size; ++i) {
                TreeNode* node = q.front();
                q.pop();

                // 核心：找到第一个叶子节点，立即返回深度
                if (node->left == nullptr && node->right == nullptr) {
                    return depth; 
                }

                if (node->left) {
                    q.push(node->left);
                }
                if (node->right) {
                    q.push(node->right);
                }
            }
            depth++;
        }
        return depth; // 理论上不会执行到这里，除非树为空
    }
};
```

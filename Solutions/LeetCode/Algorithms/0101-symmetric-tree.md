# Question

[symmetric-tree](https://leetcode.cn/problems/symmetric-tree/)



# Answer

> solution

### 交叉递归

解决该问题的思路是同步地遍历左右子树，但**交叉配对**它们的子节点：

1.  **外部调用**：主函数 `isSymmetric(root)` 仅检查根节点为空的情况，然后调用辅助函数 `isMirror(root->left, root->right)`。

2.  **镜像配对**：辅助函数 `isMirror(L, R)` 递归地进行交叉检查：
    *   **L 的左子树** 必须与 **R 的右子树**互为镜像。
    *   **L 的右子树** 必须与 **R 的左子树**互为镜像。

这种交叉递归是解决镜像对称问题的核心，体现了最大的简洁性和正确性。

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
// 辅助函数：检查 left 和 right 是否互为镜像
static bool isMirror(struct TreeNode *left, struct TreeNode *right) {
    if (left == NULL && right == NULL) {
        return true;
    }
    if (left == NULL || right == NULL || left->val != right->val) {
        return false;
    }

    // 交叉递归
    return isMirror(left->left, right->right) && isMirror(left->right, right->left);
}

bool isSymmetric(struct TreeNode* root) {
    if (root == NULL) {
        return true;
    }
    return isMirror(root->left, root->right);
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
    // 检查 p 和 q 是否互为镜像
    bool isMirror(TreeNode* p, TreeNode* q) {
        // 1. 边界条件：都为空，对称
        if (p == nullptr && q == nullptr) {
            return true;
        }
        // 2. 失败条件：一个为空，或值不相等
        if (p == nullptr || q == nullptr || p->val != q->val) {
            return false;
        }
        
        // 3. 递归检查镜像关系
        // p的左子树 必须与 q的右子树 对称 (dfs(L->L, R->R))
        // p的右子树 必须与 q的左子树 对称 (dfs(L->R, R->L))
        return isMirror(p->left, q->right) && isMirror(p->right, q->left);
    }

public:
    bool isSymmetric(TreeNode* root) {
        if (root == nullptr) {
            return true;
        }
        // 核心检查从根节点的左右子树开始
        return isMirror(root->left, root->right);
    }
};
```

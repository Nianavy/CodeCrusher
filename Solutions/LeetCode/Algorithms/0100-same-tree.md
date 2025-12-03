# Question

[same-tree](https://leetcode.cn/problems/same-tree/)



# Answer

> solution

### 结构化递归

解决该问题的思路是同步地遍历两棵树，并使用逻辑与 (`&&`) 来确保左右子树同时匹配：

1.  **边界处理**：
    *   首先处理两个指针都到达 `NULL` 的情况，返回 `true`。
    *   然后合并处理所有失败条件：如果任一指针为 `NULL`，或当前节点值不相等，返回 `false`。
2.  **递归检查**：
    *   如果当前节点值相等，则递归地检查它们的左子树 **AND** 递归地检查它们的右子树。只有当这两项检查都返回 `true` 时，整个函数才返回 `true`。

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
bool isSameTree(struct TreeNode* p, struct TreeNode* q) {
    // 1. 边界条件
    if (p == NULL && q == NULL) {
        return true;
    }
    // 只有一个为空，或者值不相等
    if (p == NULL || q == NULL || p->val != q->val) {
        return false;
    }
    
    // 2. 递归检查
    return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
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
    bool isSameTree(TreeNode* p, TreeNode* q) {
        // 1. 边界条件（Base Case）
        // 如果 p 和 q 都为空，返回 true
        if (p == nullptr && q == nullptr) {
            return true;
        }
        // 如果只有一个为空，或者值不相等，返回 false
        if (p == nullptr || q == nullptr || p->val != q->val) {
            return false;
        }
        
        // 2. 递归：检查左右子树是否同时相同
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

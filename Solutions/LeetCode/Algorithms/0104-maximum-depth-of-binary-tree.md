# Question

[maximum-depth-of-binary-tree](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)



# Answer

> solution

解决二叉树最大深度的思路，核心就两点：**递归定义**和**简洁的基线条件**。

### 1. 递归定义：自我指涉

*   一个节点的深度等于其左右子树深度的最大值，**再加 1**（加 1 是因为当前节点贡献了一层深度）。
*   公式：`Depth(root) = 1 + max(Depth(root->left), Depth(root->right))`。

### 2. 基线条件：停止点

*   递归必须停止。树的深度定义中，空节点（`NULL` 或 `nullptr`）不贡献深度。
*   停止点：如果节点为空，返回 0。

### 3. 品味和实践 (如何写得像个专业人士)

优秀的实现思路在于将上述两点结合起来，消除一切多余的噪音：

*   **消除临时变量：** 不要像 C 版本那样引入 `l` 和 `r`。直接在 `return` 语句中计算 `max()`。
*   **消除冗余控制流：** `if (!root)` 应当简洁，不要加多余的花括号。
*   **实用主义选择：** 在 C++ 中，直接使用标准库的 `max()`。在 C 中，使用三元运算符代替 `max()` 宏以避免潜在的副作用，但要保持计算的直观性。

**最终总结：**

**计算深度，本质上是问：沿着哪个方向走得最远？递归提供了一个直接的映射。你只需要定义好边界（空节点为 0）和递进关系（当前深度 = 1 + 最大子深度）。剩下的，就是追求代码上的简洁，让代码直接喊出它的意图。**

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
int maxDepth(struct TreeNode* root) {
    if (root == NULL)
        return 0;
    
    int left_depth = maxDepth(root->left);
    int right_depth = maxDepth(root->right);

    // 避免引入额外的 max() 宏或函数调用，保持代码自包含且清晰。
    return 1 + (left_depth > right_depth ? left_depth : right_depth);
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
    int maxDepth(TreeNode* root) {
        // 边界处理和主逻辑的完美结合。
        if (!root)
            return 0;
        
        return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
};
```

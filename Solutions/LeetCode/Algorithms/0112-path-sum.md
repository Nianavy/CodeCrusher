# Question

[path-sum](https://leetcode.cn/problems/path-sum/)



# Answer

> solution

## 检查路径和 (Has Path Sum)

### 1. 算法核心：深度优先搜索 (DFS)

查找路径是一个典型的 DFS 任务。我们采用递归方法，同时进行两个操作：**沿着树向下移动**，和**沿着目标和向下递减**。

**关键思想**：我们将目标和 `targetSum` 作为状态的一部分向下传递。每个节点都从 `targetSum` 中减去自己的值 (`root->val`)，然后将剩余的 `remaining_sum` 传递给子节点。

### 2. 递归状态的精确划分

成功的路径检查必须精确区分以下三种状态，以避免将非叶子节点的路径视为有效结果：

#### 状态 A: 基础停止条件（非路径）

*   **条件**：`root` 为 `NULL`。
*   **结果**：返回 `false`。空节点不能构成有效路径，它只是路径的断点。

#### 状态 B: 成功停止条件（叶子节点）

*   **条件**：当前节点是叶子节点 (`root->left == NULL && root->right == NULL`)，并且当前的 `remaining_sum` 恰好为 0。
*   **结果**：返回 `true`。我们找到了一个从根到叶子的完整路径，且和满足要求。

#### 状态 C: 继续探索（非叶子节点）

*   **条件**：当前节点不是叶子节点。
*   **操作**：递归调用左子树和右子树，传递新的 `remaining_sum`。
*   **结果**：只要左子树的搜索结果为 `true`，**或者**右子树的搜索结果为 `true`，则返回 `true`。

### 3. 实现上的简洁性

由于我们让递归函数自身处理 `NULL` 情况（返回 `false`），因此在继续探索时，我们不需要额外的 `if (root->left)` 检查。代码可以简洁地表示为：

```
return hasPathSum(root->left, remaining_sum) || hasPathSum(root->right, remaining_sum);
```

这依赖于 C/C++ 的短路评估特性：如果左侧返回 `true`，右侧就不会被执行，保证了效率和简洁性。

**最终结论**：路径和检查通过 $O(N)$ 的 DFS 实现，核心在于递归函数必须在递减目标和的同时，精确判断何时到达了**有效的叶子节点**，而不是仅仅到达了空节点。

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
bool hasPathSum(struct TreeNode* root, int targetSum) {
    // 状态 1：空节点
    if (root == NULL) {
        return false;
    }

    // 计算剩余需要的和
    int remaining_sum = targetSum - root->val;

    // 状态 2：叶子节点成功匹配
    if (root->left == NULL && root->right == NULL) {
        return remaining_sum == 0;
    }

    // 状态 3：继续探索
    // 注意：如果左子树或右子树不存在，递归调用会遇到状态 1 并返回 false，
    // 因此这里不需要额外的 NULL 检查。
    return hasPathSum(root->left, remaining_sum) || 
           hasPathSum(root->right, remaining_sum);
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
    bool hasPathSum(TreeNode* root, int targetSum) {
        // 状态 1：空节点（非路径）
        if (root == nullptr) {
            return false;
        }

        // 计算剩余需要的和
        int remaining_sum = targetSum - root->val;

        // 状态 2：叶子节点成功匹配
        // 必须检查它是否为叶子节点 (双空子树) 
        if (root->left == nullptr && root->right == nullptr) {
            return remaining_sum == 0;
        }

        // 状态 3：继续探索
        // 只要左子树或右子树中有一条路径成功，则返回 true
        return hasPathSum(root->left, remaining_sum) || 
               hasPathSum(root->right, remaining_sum);
    }
};
```

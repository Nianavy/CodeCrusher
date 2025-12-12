# Question

[balanced-binary-tree](https://leetcode.cn/problems/balanced-binary-tree/)



# Answer

> solution

## 检查二叉树平衡性

### 1. 算法核心： $O(N)$ 的单次深度优先搜索 (DFS)

判断平衡性的暴力解法是 $O(N^2)$，因为对每个节点，都需要单独计算其左右子树的高度，导致重复计算。

**高效 $O(N)$ 方案**：将两个独立的需求——**计算高度**和**检查平衡性**——合并到一个 DFS 递归函数中完成。

#### A. 函数的双重职责

递归函数（例如 `check_height`）必须执行两个任务：

1.  **返回值 (输出)**：返回当前子树的精确高度。
2.  **副作用/引用 (状态)**：通过指针或引用修改一个外部布尔变量，标记是否已发现失衡。

#### B. 核心逻辑链条

1.  **基线条件**：空节点高度为 0。
2.  **递归计算**：递归调用自身，获取左子树高度 (`lh`) 和右子树高度 (`rh`)。
3.  **检查平衡性**：在当前节点，计算 $|lh - rh|$。
    *   如果高度差 $> 1$，则立即将外部平衡状态标记为 `false`。
4.  **返回高度**：返回 `max(lh, rh) + 1` 作为当前树的高度。

### 2. 实用主义优化：早期剪枝

为了最大化效率，我们必须实现**早期剪枝（Short-circuiting）**。

*   **优化点**：一旦在树的任何深层节点发现失衡（即 `balance = false`），后续的递归调用将不再需要进行复杂的计算和深度探索。
*   **实现**：在递归函数的入口处添加判断：`if (!balance || root == NULL)`，如果平衡状态已是 `false`，则立即返回 0，无需继续向下遍历，从而节省了大量时间。

### 3. C/C++ 的实现品味

*   **C++ 风格**：使用**引用** `bool& balance` 来清晰地传递平衡状态。这比使用类成员变量作为全局状态更纯净，更安全。
*   **C 风格**：使用**指针** `bool *balance` 来实现引用传递的效果。同时，使用 C 语言的宏或三元运算符来代替 `std::max` 和 `std::abs`，保持代码的斯巴达式简洁。

**最终结论**： $O(N)$ 解决方案通过巧妙地合并计算任务，避免了重复工作，是解决平衡树检查问题的标准且高效的思路。

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
// 辅助函数：返回树的高度，并通过指针修改平衡状态
static int check_height(struct TreeNode* root, bool *is_balanced)
{
    // 如果已经发现失衡，或者到达空节点，立即返回 0 进行剪枝
    if (!(*is_balanced) || root == NULL) {
        return 0;
    }

    // 递归获取左右子树高度
    int left_h = check_height(root->left, is_balanced) + 1;
    int right_h = check_height(root->right, is_balanced) + 1;

    // 如果当前仍是平衡的，则进行检查
    if (*is_balanced) {
        // 使用宏/三元运算符代替 abs
        int diff = left_h > right_h ? left_h - right_h : right_h - left_h;
        
        if (diff > 1) {
            *is_balanced = false;
        }
    }
    
    // 返回当前节点的最大高度
    return left_h > right_h ? left_h : right_h;
}

bool isBalanced(struct TreeNode* root) {
    bool balance = true;
    check_height(root, &balance);
    return balance;
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
    // 返回树的高度，并通过引用 'balance' 传递平衡状态
    int get_height_and_check(TreeNode *root, bool& balance) {
        // 如果已经失衡，或者到达空节点，立即返回 0
        if (!balance || root == nullptr) {
            return 0;
        }
        
        // 递归计算左右子树的高度
        int left_height = get_height_and_check(root->left, balance) + 1;
        int right_height = get_height_and_check(root->right, balance) + 1;
        
        // 如果当前仍是平衡的，则进行检查
        if (balance) {
            // abs(left_height - right_height) <= 1
            if (std::abs(left_height - right_height) > 1) {
                balance = false;
            }
        }
        
        return std::max(left_height, right_height);
    }

public:
    bool isBalanced(TreeNode* root) {
        bool balance = true;
        get_height_and_check(root, balance);
        return balance;
    }
};
```

# Question

[convert-sorted-array-to-binary-search-tree](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)



# Answer

> solution

## 有序数组到平衡 BST

### 1. 算法核心：分治递归与中点策略

要将一个有序数组转换为**高度平衡**的 BST，我们必须确保树的左右子树高度差最小。由于输入数组已经有序，这使得问题变得简单：

*   **BST 特性**：左子树的所有值小于根，右子树的所有值大于根。
*   **平衡特性**：为了平衡，左右子树的节点数量必须尽可能相等。

**核心思路**：选择数组的**中间元素**作为当前子树的根节点。

**递归逻辑链条：**

1.  **基线条件**：如果当前处理的数组段的起始索引 `lo` 大于结束索引 `hi`，说明子数组为空，返回 `NULL`/`nullptr`。
2.  **确定根**：计算 `mid` 索引，选择 `nums[mid]` 作为根节点的值，并创建新节点。
3.  **分治**：
    *   **左子树**：对 `nums[lo...mid-1]` 范围进行递归调用，结果作为当前根的左子节点。
    *   **右子树**：对 `nums[mid+1...hi]` 范围进行递归调用，结果作为当前根的右子节点。

### 2. C/C++ 的关键差异和品味标准

在这个问题中，算法本身没有差异，但实现质量完全取决于两个细节：**中间点计算**和**内存管理**。

#### A. 编程品味：安全的中间点计算

这是衡量代码质量的首要标准。

*   **错误写法**：`l + r >> 1` 或 `(l + r) / 2`
    *   **缺陷**：当 `l` 和 `r` 很大时，`l + r` 可能导致整数溢出。
*   **正确写法**：`l + (r - l) / 2`
    *   **优点**：先计算距离，再偏移，保证了在任何边界情况下都不会发生溢出。这是在内核代码中强制执行的实用主义标准。

#### B. C++ 解决思路：封装与安全

*   **封装**：将递归实现封装在私有方法中，保持公共接口简洁。
*   **指针**：使用 `nullptr` 而非 C 风格的 `NULL`。
*   **内存**：使用 `new` 创建节点，依赖 C++ 环境的 RAII 或其他机制（如智能指针，如果环境允许）来处理内存生命周期，避免手动管理。

#### C. C 解决思路：内存契约的强制

*   **重点**：C 版本的功能性完全被内存管理的责任所定义。
*   **内存契约**：每次 `malloc` 之后，都必须提供明确的 `free` 机制。对于这种构造新数据结构（树）的函数，如果调用者不提供清理函数（如 `freeTree`），就会导致致命的内存泄漏。
*   **结论**：在 C 中，一个合格的解决方案不仅要构建树，还必须考虑如何安全、完整地销毁它。

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
// 递归构建函数
static struct TreeNode *partition(int *nums, int lo, int hi)
{
    if (lo > hi) {
        return NULL;
    }
    
    // 安全的中间点计算
    int mid = lo + (hi - lo) / 2; 
    
    struct TreeNode *node = malloc(sizeof(*node));
    if (!node) exit(EXIT_FAILURE); // C 语言中，内存失败通常是致命的
    
    node->val = nums[mid];
    
    node->left = partition(nums, lo, mid - 1);
    node->right = partition(nums, mid + 1, hi);
    
    return node;
}

struct TreeNode* sortedArrayToBST(int* nums, int numsSize)
{
    if (numsSize <= 0) {
        return NULL;
    }
    return partition(nums, 0, numsSize - 1);
}

// 必须提供的清理函数
void freeTree(struct TreeNode* root) {
    if (root == NULL) return;
    freeTree(root->left);
    freeTree(root->right);
    free(root);
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
    // 使用安全的中间点计算：lo + (hi - lo) / 2，避免 lo+hi 溢出
    TreeNode *build_bst(const std::vector<int>& nums, int lo, int hi) {
        if (lo > hi) {
            return nullptr;
        }
        
        // 核心：中间点作为根
        int mid = lo + (hi - lo) / 2;
        TreeNode *root = new TreeNode(nums[mid]);
        
        // 左半部分形成左子树
        root->left = build_bst(nums, lo, mid - 1);
        
        // 右半部分形成右子树
        root->right = build_bst(nums, mid + 1, hi);
        
        return root;
    }

public:
    TreeNode* sortedArrayToBST(std::vector<int>& nums) {
        return build_bst(nums, 0, nums.size() - 1);
    }
};
```

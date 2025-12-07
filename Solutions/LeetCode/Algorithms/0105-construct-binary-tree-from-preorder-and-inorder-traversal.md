# Question

[construct-binary-tree-from-preorder-and-inorder-traversal](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)



# Answer

> solution

## 重建二叉树的解决思路

### 1. 算法核心：递归与边界定位

这个问题本质上是递归的。每次递归的目标是：**找到当前子树的根，然后精确地划分子数组，让左右子树去处理它们自己的部分。**

**前序数组**提供了**根节点**（永远在当前子数组的第一个位置）。

**中序数组**提供了**分界线**。一旦找到根节点在中序数组中的位置，我们就知道根左边的一切都属于左子树，右边的一切都属于右子树。

**关键数据**：从根节点在中序数组中的位置，我们计算出**左子树的长度**。这个长度是连接前序数组和中序数组的唯一桥梁。

**递归逻辑链条：**
1.  **基线条件**：如果当前处理的数组边界无效（起始索引大于结束索引），立即返回 `NULL`。
2.  **确定根**：使用前序数组的第一个元素创建新的树节点。
3.  **定位**：查找这个根节点在中序数组中的索引。
4.  **计算长度**：`左子树长度 = 根在中序中的索引 - 中序起始索引`。
5.  **划分边界**：利用这个长度，精确计算左右子树在前序数组中的新边界。例如，左子树在前序数组中结束于 `前序起始索引 + 左子树长度`。
6.  **递归**：对左右子树的新边界进行递归调用。

### 2. C++ 的思路：性能至上与 $O(N)$ 优化

在 C++ 中，我们追求工业级的速度和效率。

**决策点**：**查找定位**是算法的瓶颈。如果每次递归都线性查找 $O(N)$，总复杂度会是 $O(N^2)$。

**C++ 解决方案**：毫不犹豫地使用现代语言工具。在算法开始前，通过 `std::unordered_map` 对中序数组进行预处理，将所有**元素值**映射到它们在数组中的**索引**。

**结果**：定位根节点的操作降为 $O(1)$。代码保持清晰，并且通过 `new` 和 `std::vector` 提高了安全性和可读性。简洁性通过精确定义长度变量（如 `left_subtree_length`）来保证，避免了混乱的边界计算。

### 3. C 的思路：结构简洁与内存安全

在 C 语言中，我们必须权衡效率和代码的复杂性。

**决策点**：如果要实现 $O(N)$ 性能，需要自己构建一个复杂的哈希表并处理其生命周期和内存泄漏。这是**过度工程**，且引入了巨大的风险。

**C 解决方案**：遵循斯巴达式简洁的哲学。避免引入任何复杂的、需要 `malloc` / `free` 额外清理的辅助数据结构。

**权衡**：我们接受 $O(N^2)$ 的时间复杂度，通过在线性时间内（`find_root_inorder`）查找根节点的位置。这样可以保证代码结构极其简单，只分配树节点所需的内存，并且避免了因复杂哈希表导致的各种内存管理错误。

**原则**：C 语言的哲学是，**代码越少，Bug 越少。** 宁愿慢一点，也要保证内存和结构上的绝对清晰。边界计算必须像 C++ 一样，通过计算长度变量来保证可读性。

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
// 辅助函数：在线性时间内查找根节点在中序数组中的位置。
// 注意：这是 O(N) 操作，导致整个重建过程是 O(N^2)
static int find_root_inorder(int root_val, int* inorder, int in_low, int in_high)
{
    // 遍历中序数组的有效范围 [in_low, in_high]
    for (int i = in_low; i <= in_high; i++) {
        if (inorder[i] == root_val) {
            return i;
        }
    }
    // 不应该发生，但如果发生，返回一个非法索引
    return -1; 
}

// 核心递归函数
static struct TreeNode* buildTree_recursive(
    int* preorder, int pre_low, int pre_high,
    int* inorder, int in_low, int in_high)
{
    // 基线条件：当前子树的边界无效
    if (pre_low > pre_high || in_low > in_high) {
        return NULL;
    }

    // 1. 根节点是前序数组的第一个元素
    int root_val = preorder[pre_low];
    struct TreeNode *root = malloc(sizeof(*root));
    if (!root) {
        // 在 C 语言中，如果内存分配失败，必须处理或立即终止
        return NULL; // 简单处理
    }
    root->val = root_val;
    root->left = NULL;
    root->right = NULL;

    // 2. 查找根节点在中序数组中的位置 (O(N) 查找)
    int root_inorder_index = find_root_inorder(root_val, inorder, in_low, in_high);
    
    // 如果值不存在，说明输入数据有误，这里简单返回 NULL
    if (root_inorder_index == -1) {
        free(root);
        return NULL;
    }
    
    // 3. 计算左子树的长度
    int left_subtree_length = root_inorder_index - in_low;

    // 4. 递归构建左子树
    // 前序边界：[pre_low + 1] 到 [pre_low + left_subtree_length]
    // 中序边界：[in_low] 到 [root_inorder_index - 1]
    root->left = buildTree_recursive(
        preorder,
        pre_low + 1,
        pre_low + left_subtree_length,
        inorder,
        in_low,
        root_inorder_index - 1
    );

    // 5. 递归构建右子树
    // 前序边界：[pre_low + left_subtree_length + 1] 到 [pre_high]
    // 中序边界：[root_inorder_index + 1] 到 [in_high]
    root->right = buildTree_recursive(
        preorder,
        pre_low + left_subtree_length + 1,
        pre_high,
        inorder,
        root_inorder_index + 1,
        in_high
    );

    return root;
}

// 外部接口函数
struct TreeNode* buildTree(int* preorder, int preorderSize, int* inorder, int inorderSize) {
    if (preorderSize != inorderSize || preorderSize == 0) {
        return NULL;
    }
    
    // 启动递归，使用数组的完整边界
    return buildTree_recursive(
        preorder, 0, preorderSize - 1,
        inorder, 0, inorderSize - 1
    );
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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        // 1. 预处理：O(N) 建立中序索引查找表，这是 O(N log N) 算法的关键优化。
        for (int i = 0; i < inorder.size(); i++) {
            map_[inorder[i]] = i;
        }
        int size = inorder.size();
        return dfs(preorder, 0, size - 1, inorder, 0, size - 1);
    }

private:
    std::unordered_map<int, int> map_; 

    // 递归函数：使用清晰的 low/high 边界。
    TreeNode* dfs(vector<int>& preorder, int pre_lo, int pre_hi, 
                  vector<int>& inorder, int in_lo, int in_hi) {
        
        // 1. 基线条件：边界重叠或无效
        if (pre_lo > pre_hi) { 
            return nullptr;
        }

        // 2. 根：前序遍历的第一个元素
        int root_val = preorder[pre_lo];
        TreeNode *root = new TreeNode(root_val);
        
        // 3. 查找根在中序中的位置
        int root_inorder_index = map_[root_val];
        
        // 4. 关键：计算左子树的长度
        int left_subtree_length = root_inorder_index - in_lo;

        // 5. 递归构建左子树
        root->left = dfs(preorder, 
                         pre_lo + 1,                            // 前序：跳过根
                         pre_lo + left_subtree_length,          // 前序：从 pre_lo+1 开始，长度为 left_subtree_length
                         inorder, 
                         in_lo, 
                         root_inorder_index - 1);               // 中序：根左边的一切

        // 6. 递归构建右子树
        root->right = dfs(preorder, 
                          pre_lo + left_subtree_length + 1,     // 前序：左子树结束后的下一个位置
                          pre_hi, 
                          inorder, 
                          root_inorder_index + 1,               // 中序：根右边的一切
                          in_hi);
                          
        return root;
    }
};
```

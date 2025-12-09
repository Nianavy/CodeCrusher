# Question

[construct-binary-tree-from-inorder-and-postorder-traversal](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)



# Answer

> solution

### 1. 算法核心：从后序确定根，从中序计算长度

解决思路依然基于递归，利用后序遍历和中序遍历的特性精确地将数组切分成左右子树的输入。

#### A. 根节点的确定 (Postorder)

*   **后序遍历的特性**：后序数组的**最后一个元素**永远是当前子树的根节点。
*   **作用**：每次递归，我们都从后序数组的末尾获取根节点的值。

#### B. 数组的分割与长度计算 (Inorder)

*   **中序遍历的作用**：在中序数组中找到根节点的位置（`index`）。
*   **关键指标**：计算根左边元素的数量，即**左子树的长度** (`left_subtree_length = index - in_low`)。

#### C. 递归边界的精确计算

这是最容易出错的部分，必须通过长度进行推导。

*   **左子树**：
    *   中序：从 `in_low` 到 `index - 1`。
    *   后序：从 `post_low` 开始，到 `post_low + left_subtree_length - 1` 结束。
*   **右子树**：
    *   中序：从 `index + 1` 到 `in_hi`。
    *   后序：从 `post_low + left_subtree_length` 开始（左子树后序结束的下一个位置），到 `post_hi - 1` 结束（跳过当前根节点）。

### 2. C++ 解决思路：O(N) 性能优化

C++ 的思路是消除 $O(N^2)$ 的瓶颈，追求速度。

*   **优化手段**：使用 `std::unordered_map` 对中序数组进行预处理，实现 $O(1)$ 查找根节点在中序数组中的位置。
*   **结果**：整体算法复杂度达到 $O(N)$。

### 3. C 解决思路：结构简洁与内存安全

C 语言的思路是避免引入复杂的数据结构和内存风险。

*   **权衡**：放弃 $O(N)$ 性能优化，不使用额外的哈希表或链表结构。
*   **实现**：每次递归，通过 $O(N)$ 线性查找来定位根节点在中序数组中的位置。
*   **代价**：总时间复杂度为 $O(N^2)$。但代码结构简洁，只需要管理树节点本身的内存 (`malloc`/`free`)，最大程度避免了 C 环境下的内存错误。

### 总结

无论是 C 还是 C++，核心思路都是通过**左子树长度**作为桥梁，将中序数组的分割信息，准确地映射到后序数组的边界上。C++ 利用工具追求效率，C 则牺牲效率保证结构的纯粹和内存的安全。

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
// O(N) 线性查找，牺牲性能换取结构简洁
static int find_root_inorder(int root_val, int* inorder, int in_low, int in_high)
{
    for (int i = in_low; i <= in_high; i++) {
        if (inorder[i] == root_val) {
            return i;
        }
    }
    return -1;
}

static struct TreeNode* buildTree_recursive(
    int* inorder, int in_low, int in_high,
    int* postorder, int post_low, int post_high)
{
    if (in_low > in_high) {
        return NULL;
    }

    // 1. 根节点是后序数组的最后一个元素
    int root_val = postorder[post_high];
    struct TreeNode *root = malloc(sizeof(*root));
    if (!root) return NULL;
    
    root->val = root_val;
    root->left = NULL;
    root->right = NULL;

    // 2. 查找定位
    int root_inorder_index = find_root_inorder(root_val, inorder, in_low, in_high);
    if (root_inorder_index == -1) {
        free(root);
        return NULL;
    }
    
    // 3. 关键：计算左子树的长度
    int left_subtree_length = root_inorder_index - in_low;

    // 4. 递归构建左子树
    root->left = buildTree_recursive(
        inorder,
        in_low,
        root_inorder_index - 1,
        postorder,
        post_low,
        post_low + left_subtree_length - 1
    );

    // 5. 递归构建右子树
    root->right = buildTree_recursive(
        inorder,
        root_inorder_index + 1,
        in_high,
        postorder,
        post_low + left_subtree_length,
        post_high - 1
    );

    return root;
}

struct TreeNode* buildTree(int* inorder, int inorderSize, int* postorder, int postorderSize) {
    if (inorderSize != postorderSize || inorderSize == 0) {
        return NULL;
    }
    
    return buildTree_recursive(
        inorder, 0, inorderSize - 1,
        postorder, 0, postorderSize - 1
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
private:
    std::unordered_map<int, int> map_; // Inorder value -> index

    TreeNode* dfs(const std::vector<int>& inorder, int in_lo, int in_hi, 
                  const std::vector<int>& postorder, int post_lo, int post_hi) {
        
        if (in_lo > in_hi) { 
            return nullptr;
        }

        // 1. 根节点是后序数组的最后一个元素
        int root_val = postorder[post_hi];
        TreeNode *root = new TreeNode(root_val);
        
        // 2. 查找根在中序中的索引
        int root_inorder_index = map_[root_val];
        
        // 3. 关键：计算左子树的长度
        int left_subtree_length = root_inorder_index - in_lo;

        // 4. 递归构建左子树
        // 左子树后序结束于：post_lo + left_subtree_length - 1
        root->left = dfs(inorder, 
                         in_lo,                         // 中序：保持不变
                         root_inorder_index - 1,        // 中序：根左边
                         postorder, 
                         post_lo,                       // 后序：保持不变
                         post_lo + left_subtree_length - 1 // 后序：根据长度计算结束点
        );

        // 5. 递归构建右子树
        // 右子树后序开始于：post_lo + left_subtree_length
        root->right = dfs(inorder, 
                          root_inorder_index + 1,       // 中序：根右边
                          in_hi,                        // 中序：保持不变
                          postorder, 
                          post_lo + left_subtree_length,// 后序：左子树后序的下一个位置
                          post_hi - 1                   // 后序：跳过当前根节点
        );
                          
        return root;
    }

public:
    TreeNode* buildTree(std::vector<int>& inorder, std::vector<int>& postorder) {
        // 预处理
        for (int i = 0; i < inorder.size(); ++i) {
            map_[inorder[i]] = i;
        }
        int size = inorder.size();
        return dfs(inorder, 0, size - 1, postorder, 0, size - 1);
    }
};
```

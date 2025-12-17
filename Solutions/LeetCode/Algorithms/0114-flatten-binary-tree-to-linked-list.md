# Question

[flatten-binary-tree-to-linked-list](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)



# Answer

> solution

## 二叉树扁平化

### 1. 核心目标与约束

**目标**：将树结构转换为单链表，节点顺序必须是**先序遍历**的顺序。

**约束**：必须是**原地修改**（in-place），使用右指针作为链表连接，左指针必须设为 `NULL`。

### 2. 核心挑战：右子树的存放

先序遍历的顺序是：**根 -> 左子树 -> 右子树**。

当我们将左子树移动到根节点的右侧时，原始的右子树（应该排在最后）必须先被妥善存放。

### 3. 方案一：迭代法（莫里斯遍历变体，$O(1)$ 空间）

这是最高效的方案，因为它不需要额外的栈空间。

1.  **外部循环**：从根节点开始，迭代遍历当前处理的节点 (`current`)。
2.  **左子树检查**：如果 `current->left` 存在，说明需要进行重排。
3.  **查找连接点**：在 `current->left` 构成的子树中，找到其**最右侧的节点**（这是左子树中最后一个被访问的先序节点）。
4.  **连接**：将 `current` 原始的 `right` 子树（即应该在左子树后面的内容），接到这个最右侧节点的 `right` 指针上。
5.  **提升**：将 `current->left` 提升为 `current->right`，并清除 `current->left`。
6.  **前进**：`current` 移动到新的 `right` 子节点，继续下一轮迭代。

这种方法巧妙地利用了左子树的尾部，作为原始右子树的临时连接点，实现了 $O(1)$ 空间复杂度的原地重排。

### 4. 方案二：递归法（自底向上，$O(H)$ 空间）

这种方法利用了递归栈来隐含地记录先序遍历的顺序。

1.  **递归顺序**：函数采用类似于**后序遍历**的顺序：先递归调用右子树，再递归调用左子树。
2.  **状态返回**：递归函数返回当前子树扁平化后的**链表尾部节点**。
3.  **连接操作**：在处理当前节点时，如果左子树存在，则利用左子树返回的尾部节点，将其连接到原始的右子树的头部。
4.  **提升与清理**：然后，将左子树提升为右子树，并清除左指针。

**总结**：无论是 $O(1)$ 空间的迭代法，还是 $O(H)$ 空间的递归法，核心都在于**精确找到先序遍历中“左子树”与“右子树”的接缝处，并将原始的右子树内容接驳到这个接缝处**。迭代法通过指针操作实现了精巧的 $O(1)$ 空间效率。

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
static struct TreeNode *partition(struct TreeNode *node)
{
    if (node == NULL) {
        return NULL;
    }

    if (node->right == NULL && node->left == NULL) {
        return node; // 递归返回当前子树的最后一个节点
    }

    struct TreeNode *right_last = partition(node->right); // 递归先扁平化右侧
    struct TreeNode *left_last = partition(node->left);   // 递归扁平化左侧

    if (left_last != NULL) {
        // 核心连接：将左子树的最后一个节点连接到原始右子树
        left_last->right = node->right;
        
        // 提升：将左子树整体提升为右子树
        node->right = node->left;
        
        // 清理左指针
        node->left = NULL;
    }
    
    // 返回：当前子树扁平化后的最后一个节点
    return right_last != NULL ? right_last : (left_last != NULL ? left_last : node);
}

static void flatten(struct TreeNode *root)
{
    partition(root);
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
    void flatten(TreeNode* root) {
        TreeNode* current = root;
        
        while (current) {
            // 如果左子树存在
            if (current->left) {
                
                // 1. 找到左子树的最右节点 (Predecessor)
                TreeNode* predecessor = current->left;
                while (predecessor->right) {
                    predecessor = predecessor->right;
                }
                
                // 2. 将原始的右子树（current->right）接到 predecessor 的右侧
                predecessor->right = current->right;
                
                // 3. 将左子树整体提升为新的右子树
                current->right = current->left;
                
                // 4. 清空左指针
                current->left = nullptr; 
            }
            
            // 移动到下一个节点（新的右子节点，即原左子树的根）
            current = current->right;
        }
    }
};
```

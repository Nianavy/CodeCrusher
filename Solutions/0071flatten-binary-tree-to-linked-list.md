https://leetcode.cn/problems/flatten-binary-tree-to-linked-list

### Think
```
初始化指针：
TreeNode *curr = root;：从根节点开始遍历。

遍历二叉树：
while (curr != nullptr)：循环遍历当前节点，直到当前节点为空。

处理左子树：
if (curr->left != nullptr)：如果当前节点有左子树，则需要进行展开操作。
auto next = curr->left;：将当前节点的左子树保存为next。
auto predecessor = next;：找到next的最右节点（即左子树的最右节点）。
while (predecessor->right != nullptr) predecessor = predecessor->right;：循环找到左子树的最右节点。
predecessor->right = curr->right;：将当前节点的右子树接到左子树的最右节点上。
curr->left = nullptr;：将当前节点的左子树置为空。
curr->right = next;：将左子树变为当前节点的右子树。

移动到下一个节点：
curr = curr->right;：移动到下一个节点（即原来的左子树）。
```

### Ways
By C++
```C++
class Solution {
public:
    void flatten(TreeNode* root) {
        TreeNode *curr = root;
        while (curr != nullptr) {
            if (curr->left != nullptr) {
                auto next = curr->left;
                auto predecessor = next;
                while (predecessor->right != nullptr) predecessor = predecessor->right;
                predecessor->right = curr->right;
                curr->left = nullptr;
                curr->right = next;
            }
            curr = curr->right;
        }
    }
};
```

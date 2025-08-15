https://leetcode.cn/problems/invert-binary-tree

### Think
```TXT
Recursive
```

### Ways
By C++
```C++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) return nullptr;
        TreeNode *left = invertTree(root->left);
        TreeNode *right = invertTree(root->right);
        root->left = right;
        root->right = left;
        return root;
    }
};
```

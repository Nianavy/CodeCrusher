https://leetcode.cn/problems/balanced-binary-tree

### Think
```
Recursive of the Top to Bottom
```

### Ways
By C++
```C++
class Solution {
public:
    int height(TreeNode *root) {
        if (root == NULL) return 0;
        else return max(height(root->left), height(root->right)) + 1;
    }
    bool isBalanced(TreeNode* root) {
        if (root == NULL) return true;
        else return abs(height(root->left) - height(root->right)) <= 1 && isBalanced(root->left) && isBalanced(root->right);
    }
};
```

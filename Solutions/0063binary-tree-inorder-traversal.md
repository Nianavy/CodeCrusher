https://leetcode.cn/problems/binary-tree-inorder-traversal

### Think
```TXT
recursive
```

### Ways
By C++
```C++
class Solution {
public:
    void inorder(TreeNode *root, vector<int> &res) {
        if (!root) return;
        inorder(root->left, res);
        res.push_back(root->val);
        inorder(root->right, res);
    }
    vector<int> inorderTraversal(TreeNode *root) {
        vector<int> res;
        inorder(root, res);
        return res;        
    }
};
```

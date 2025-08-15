https://leetcode.cn/problems/binary-tree-postorder-traversal

### Think
```TXT
recursive
```

### Ways
By C++
```C++
class Solution {
public:
    void postorder(TreeNode *root, vector<int> &res) {
        if (root == nullptr) return;
        postorder(root->left, res);
        postorder(root->right, res);
        res.push_back(root->val);
    }
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        postorder(root, res);
        return res;
    }
};
```

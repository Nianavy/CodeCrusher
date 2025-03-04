https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree

### Think
```
one for-each
```

### Ways
By C++
```C++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode *ancestor = root;
        while (true) {
            if (p->val < ancestor->val && q->val < ancestor->val) ancestor = ancestor->left;
            else if (p->val > ancestor->val && q->val > ancestor->val) ancestor = ancestor->right;
            else break;
        }
        return ancestor;
    }
};
```

https://leetcode.cn/problems/maximum-depth-of-binary-tree

### Think
```
dfs
```
### Ways
By C++
```C++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

https://leetcode.cn/problems/find-bottom-left-tree-value

### Think
```
bfs
```

### Ways
By C++
```C++
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        int ret;
        queue<TreeNode *> q;
        q.push(root);
        while (!q.empty()) {
            auto p = q.front();
            q.pop();
            if (p->right) q.push(p->right);
            if (p->left) q.push(p->left);
            ret = p->val;
        }
        return ret;
    }
};
```

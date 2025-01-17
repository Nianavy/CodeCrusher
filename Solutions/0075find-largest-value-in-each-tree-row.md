https://leetcode.cn/problems/find-largest-value-in-each-tree-row

### Think
```
dfs
```

### Ways
By C++
```C++
class Solution {
public:
    void dfs(vector<int> &res, TreeNode *root, int curHeight) {
        if (curHeight == res.size()) res.push_back(root->val);
        else res[curHeight] = max(res[curHeight], root->val);
        if (root->left) dfs(res, root->left, curHeight + 1);
        if (root->right) dfs(res, root->right, curHeight + 1);
    }
    vector<int> largestValues(TreeNode *root) {
        if (!root) return {};
        vector<int> res;
        dfs(res, root, 0);
        return res;
    }
};
```

https://leetcode.cn/problems/binary-tree-paths

### Think
```
dfs
```

### Ways
By C++
```C++
class Solution {
public:
    void construct_paths(TreeNode *root, string path, vector<string> &paths) {
        if (root != nullptr) {
            path += to_string(root->val);
            if (root->left == nullptr && root->right == nullptr) paths.push_back(path);
            else {
                path += "->";
                construct_paths(root->left, path, paths);
                construct_paths(root->right, path, paths);
            }
        }
    }
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> paths;
        construct_paths(root, "", paths);
        return paths;
    }
};
```

https://leetcode.cn/problems/maximum-width-of-binary-tree

### Think
```
bfs
```

### Ways
By C++
```C++
class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        unsigned long long res = 1;
        vector<pair<TreeNode *, unsigned long long>> arr;
        arr.emplace_back(root, 1L);
        while (!arr.empty()) {
            vector<pair<TreeNode *, unsigned long long>> tmp;
            for (auto &[node, index] : arr) {
                if (node->left) tmp.emplace_back(node->left, index * 2);
                if (node->right) tmp.emplace_back(node->right, index * 2 + 1);
            }
            res = max(res, arr.back().second - arr[0].second + 1);
            arr = move(tmp);
        }
        return res;
    }
};
```

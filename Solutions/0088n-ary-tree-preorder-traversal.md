https://leetcode.cn/problems/n-ary-tree-preorder-traversal

### Think
```
Recursive
```

### Ways
By C++
```C++
class Solution {
public:
    void helper(const Node *root, vector<int> &res) {
        if (root == nullptr) return;
        res.emplace_back(root->val);
        for (auto &ch : root->children) helper(ch, res);
    }
    vector<int> preorder(Node* root) {
        vector<int> res;
        helper(root, res);
        return res;
    }
};
```

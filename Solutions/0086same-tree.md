https://leetcode.cn/problems/same-tree

### Think
```
DFS
```

### Ways
By C++
```C++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == nullptr && q == nullptr) return true;
        else if (p == nullptr || q == nullptr) return false;
        else if (p->val != q->val) return false;
        else return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

https://leetcode.cn/problems/LwUNpT/description/?envType=problem-list-v2&envId=G25w0aD1

### Think
```
DFS  
```

### Ways
By C++
```C++
class Solution {
public:
    void dfs(TreeNode *root, int height, int &curVal, int &curHeight) {
        if (root == nullptr) return;
        ++height;
        dfs(root->left, height, curVal, curHeight);
        dfs(root->right, height, curVal, curHeight);
        if (height > curHeight) {
            curHeight = height;
            curVal = root->val;
        }
    }
    int findBottomLeftValue(TreeNode* root) {
        int curVal, curHeight = 0;
        dfs(root, 0, curVal, curHeight);
        return curVal;
    }
};
```

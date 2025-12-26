# Question

[binary-tree-maximum-path-sum](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)



# Answer

> solution

把「任何以当前节点必经过的单条上升路径」与「以它为拐点连通左右子树的完整路径」分开，做一次后序 DFS，两遍就行。

拆分说明：

对每个节点算 左右子树能贡献的最大单边链（负值就砍掉当 0）。
用两边贡献之和 + 当前值获得 经过该节点的可能全局最大值，随时更新全局。
向上返回只能选单边，因为得保证“单条链”性质。
复杂度：

时间 O(N)，每个节点只进栈一次；空间 O(H)（递归栈高）。零冗余、零特殊情况。

### Way Of C

> source code

```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */
int dfs(struct TreeNode* n, int* mx) {
    if (!n) return 0;
    int l = dfs(n->left, mx);
    int r = dfs(n->right, mx);
    if (l < 0) l = 0;
    if (r < 0) r = 0;
    int sum = n->val + l + r;
    if (sum > *mx) *mx = sum;
    return n->val + (l > r ? l : r);
}
int maxPathSum(struct TreeNode* root) {
    int mx = INT_MIN;
    dfs(root, &mx);
    return mx;
}
```

---

### Way Of C++

> source code

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        int mx = INT_MIN;
        dfs(root, mx);
        return mx;
    }
private:
    int dfs(TreeNode* n, int& mx) {
        if (!n) return 0;
        int l = max(0, dfs(n->left, mx));
        int r = max(0, dfs(n->right, mx));
        mx = max(mx, n->val + l + r);
        return n->val + max(l, r);
    }
};
```

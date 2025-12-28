# Question

[sum-root-to-leaf-numbers](https://leetcode.cn/problems/sum-root-to-leaf-numbers/)



# Answer

> solution

【问题本质】
从根到叶的所有路径，每条路径的数字组成一个数，求所有这些数的和。

【解决思路】

第一层：算法选择
DFS前序遍历。从根到叶，逐层累积数字。

第二层：数据结构
一个整数 sum，在递归栈中传递：
sum = sum * 10 + node->val：每层追加一位数字
到达叶子节点时，sum 就是完整的路径数字

第三层：核心逻辑
1. 递归遍历每个节点
2. 累积当前路径的数字：sum = sum * 10 + node->val
3. 叶子节点：返回 sum（这条路径的数字）
4. 非叶子节点：返回左子树之和 + 右子树之和

第四层：为什么这样做
不需要全局变量或额外数据结构
sum 在递归栈中自动保存每条路径的状态
每个节点访问一次，时间O(n)，空间O(h)（h为树高）

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
static int dfs(struct TreeNode* node, int sum) {
    if (!node) return 0;
    sum = sum * 10 + node->val;
    if (!node->left && !node->right) return sum;
    return dfs(node->left, sum) + dfs(node->right, sum);
}
int sumNumbers(struct TreeNode* root) {
    if (!root) return 0;
    
    int sum = root->val;
    if (!root->left && !root->right) return sum;
    
    return dfs(root->left, sum) + dfs(root->right, sum);
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
    int sumNumbers(TreeNode* root) {
        return dfs(root, 0);
    }
private:
    int dfs(TreeNode* root, int sum) {
        if (!root) return 0;
        sum = sum * 10 + root->val;
        if (!root->left && !root->right) return sum;
        return dfs(root->left, sum) + dfs(root->right, sum);
    }
};
```

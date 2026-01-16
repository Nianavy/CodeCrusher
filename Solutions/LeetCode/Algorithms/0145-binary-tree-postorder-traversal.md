# Question

[binary-tree-postorder-traversal](https://leetcode.cn/problems/binary-tree-postorder-traversal/)



# Answer

> solution

## C++ 版本 (经典递归)
**思路**：
1.  直接使用递归函数进行后序遍历。
2.  遍历顺序：左 -> 右 -> 根。
3.  使用 `vector<int>` 动态追加结果，无需预先计算总节点数。

**特点**：
- 代码最短，逻辑最直接。
- 利用 `vector` 自动管理内存，无分配/释放错误风险。
- 递归栈深度与树高相关，评测用例通常安全。

---

## C 版本 (双递归)
**思路**：
1.  **第一次递归**：统计树中节点总数 `total`。
2.  **第二次递归**：按后序顺序（左->右->根）填充到已分配好的数组。
3.  使用两个辅助递归函数分别完成计数和填充。

**特点**：
- 无手动栈操作，避免指针越界或悬垂指针错误。
- 内存管理清晰：仅 `malloc` 一次结果数组。
- 代码结构清晰，每个函数只做一件事。
- 耗时 O(N)，空间 O(H)，适用于题目限制的树高。

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
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
// 递归计数函数
int countNodes(struct TreeNode* root) {
    if (!root) return 0;
    return 1 + countNodes(root->left) + countNodes(root->right);
}
// 递归后序遍历填充函数
void fillPostorder(struct TreeNode* node, int* results, int* index) {
    if (!node) return;
    fillPostorder(node->left, results, index);
    fillPostorder(node->right, results, index);
    results[(*index)++] = node->val;
}
// 主函数
int* postorderTraversal(struct TreeNode* root, int* returnSize) {
    if (!root) {
        *returnSize = 0;
        return NULL;
    }
    // 第一步：递归计数节点总数
    int total = countNodes(root);
    // 第二步：分配结果数组
    int* results = malloc(total * sizeof(int));
    if (!results) {
        *returnSize = 0;
        return NULL;
    }
    *returnSize = total;
    // 第三步：递归填充数组
    int index = 0;
    fillPostorder(root, results, &index);
    return results;
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
private:
    // 递归辅助函数
    void dfs(TreeNode* node, std::vector<int>& res) {
        if (node == nullptr) return;
        dfs(node->left, res);  // 左
        dfs(node->right, res); // 右
        res.push_back(node->val); // 根
    }
public:
    std::vector<int> postorderTraversal(TreeNode* root) {
        std::vector<int> res;
        dfs(root, res);
        return res;
    }
};
```

# Question

[unique-binary-search-trees-ii](https://leetcode.cn/problems/unique-binary-search-trees-ii/)



# Answer

> solution

### 基于递归的笛卡尔积生成

解决该问题的思路是，定义一个函数 `dfs(low, high)` 返回一个集合，该集合包含所有根节点在 `[low, high]` 范围内的有效 BST 的**根指针**。

1.  **基准情况（Base Case）**
    *   如果 `low > high`，表示空区间。函数必须返回一个包含 **1 个 `NULL` 指针**的集合。

2.  **根节点迭代**
    *   遍历 $i$ 从 `low` 到 `high`，将 $i$ 作为当前根节点。

3.  **递归与组合（笛卡尔积）**
    *   递归获取左子树集合 $L = dfs(low, i-1)$。
    *   递归获取右子树集合 $R = dfs(i+1, high)$。
    *   使用双重循环遍历 $L$ 和 $R$ 中的所有树根 $l$ 和 $r$：
        *   为新的根节点 $i$ **独立分配内存**。
        *   设置 `root->left = l`，`root->right = r`。
        *   将新生成的 `root` 指针添加到当前结果集。

4.  **资源管理（C 语言特有）**
    *   在 C 语言中，一旦使用完子递归返回的指针数组 (`left_subs`, `right_subs`)，必须立即 `free()` 掉该数组本身占用的内存，以避免堆栈过度膨胀。

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
// C 语言需要手动管理动态数组，使用 realloc 来扩展结果集。
// 函数返回 struct TreeNode**，即指向树根指针的数组。
static struct TreeNode** dfs_c(int low, int high, int *count) {
    if (low > high) {
        // 空区间：返回包含一个 NULL 的数组，count 为 1。
        // 这解决了 C++ 版本中 {NULL} 带来的优雅边界处理。
        struct TreeNode **results = (struct TreeNode**)malloc(sizeof(struct TreeNode*));
        if (results == NULL) { *count = 0; return NULL; }
        results[0] = NULL;
        *count = 1;
        return results;
    }

    int total_results = 0;
    int capacity = 8; // 初始容量
    struct TreeNode **results = (struct TreeNode**)malloc(capacity * sizeof(struct TreeNode*));
    if (results == NULL) { *count = 0; return NULL; }

    for (int i = low; i <= high; i++) {
        // 1. 递归获取左右子树的所有可能集合
        int left_cnt, right_cnt;
        struct TreeNode **left_subs = dfs_c(low, i - 1, &left_cnt);
        struct TreeNode **right_subs = dfs_c(i + 1, high, &right_cnt);

        // 2. 交叉组合 (笛卡尔积)
        for (int j = 0; j < left_cnt; j++) {
            for (int k = 0; k < right_cnt; k++) {
                
                // 检查容量并扩展
                if (total_results >= capacity) {
                    capacity *= 2;
                    struct TreeNode **new_results = (struct TreeNode**)realloc(results, capacity * sizeof(struct TreeNode*));
                    if (new_results == NULL) { /* 内存分配失败，需要释放已分配的子树 */ return NULL; } 
                    results = new_results;
                }
                
                // 3. 为当前根节点创建新的独立内存
                struct TreeNode *root = (struct TreeNode*)malloc(sizeof(struct TreeNode));
                if (root == NULL) { /* 内存分配失败 */ return NULL; }
                
                root->val = i;
                // 注意：left_subs/right_subs 已经包含了 NULL 指针，因此无需特殊处理。
                root->left = left_subs[j]; 
                root->right = right_subs[k]; 

                results[total_results++] = root;
            }
        }
        
        // 释放左右子集数组本身（它们是指针数组，不释放子数组内部的 TreeNode）
        // 因为我们只需要这些指针，不需要它们占据的数组空间。
        free(left_subs);
        free(right_subs);
    }
    
    // 调整最终数组大小，节省内存（可选，但好品味要求）
    struct TreeNode **final_results = (struct TreeNode**)realloc(results, total_results * sizeof(struct TreeNode*));
    if (final_results) {
        results = final_results;
    }

    *count = total_results;
    return results;
}

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
struct TreeNode** generateTrees(int n, int* returnSize) {
    if (n <= 0) {
        *returnSize = 0;
        return NULL;
    }
    // 递归调用
    return dfs_c(1, n, returnSize);
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
    vector<TreeNode*> generateTrees(int n) {
        if (!n) return {};
        return dfs(1, n);
    }

    vector<TreeNode*> dfs(int l, int r) {
        if (l > r) return {NULL}; // 优雅地返回包含一个NULL指针的vector
        vector<TreeNode*> res;

        for (int i = l; i <= r; i ++ ) { // i 作为根节点
            auto left = dfs(l, i - 1), right = dfs(i + 1, r); // 递归获取左右子树所有可能性
            
            // 交叉组合：笛卡尔积
            for (auto l: left)
                for (auto r: right) {
                    auto root = new TreeNode(i); // 为当前根节点分配新的内存
                    root->left = l, root->right = r; // 连接子树（子树指针来自递归返回）
                    res.push_back(root);
                }
        }
        return res;
    }
};
```

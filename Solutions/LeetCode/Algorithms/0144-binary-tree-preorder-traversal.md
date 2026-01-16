# Question

[binary-tree-preorder-traversal](https://leetcode.cn/problems/binary-tree-preorder-traversal/)



# Answer

> solution

## **解决思路总结**

这个问题本质是**如何用给定函数签名实现二叉树前序遍历**。前序遍历的顺序是：“根 -> 左 -> 右”。

### **核心挑战与解决方案：**

1.  **C++ 签名**：`vector<int>`
    - **好处**：无需预先知道大小。
    - **方案**：使用递归。对于每个节点，先记录值，然后递归处理左子树，最后递归处理右子树。这是最直接、最符合数学定义的实现。

2.  **C 签名**：`int*` + `returnSize`
    - **挑战**：`int*` 是固定大小的内存块，必须提前知道节点总数才能分配。
    - **方案**：**两次遍历**。
        - **第一遍**：用栈模拟递归，遍历整棵树，**只计数节点**。
        - **第二遍**：根据第一遍得到的节点数，用 `malloc` 分配正确大小的数组，再次遍历，将节点值按顺序填入数组。
    - **关键**：C 语言没有 `vector` 的自动增长特性，所以“先计数再分配数组”是处理动态大小的唯一稳健方法。

### **每种方案的本质：**
- **C++递归**：直接、清晰，利用语言特性（动态容器）。
- **C迭代**：通过“两次遍历”来模拟内存管理，确保一次分配正确大小的内存，这是底层编程中保证鲁棒性的常见模式。

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
int* preorderTraversal(struct TreeNode* root, int* returnSize) {
    // 1. 处理空树并设置返回Size
    if (root == NULL) {
        *returnSize = 0;
        return NULL;
    }
    // 2. 为了正确分配内存，**必须先遍历一次得到节点总数**
    //    这是一个必要的成本，以确保内存分配大小精确。
    int nodeCount = 0;
    // 使用一个临时栈来模拟递归，进行遍历计数
    struct TreeNode** tempStack = malloc(1000 * sizeof(struct TreeNode*)); // 假设最大深度1000
    if (!tempStack) {
        *returnSize = 0;
        return NULL;
    }
    
    struct TreeNode** top = tempStack;
    struct TreeNode* current = root;
    
    // 前序遍历的迭代计数过程
    while (current != NULL || top != tempStack) {
        if (current != NULL) {
            nodeCount++; // 访问节点
            *top++ = current; // 压栈
            current = current->left;
        } else {
            current = *--top; // 弹栈
            current = current->right;
        }
    }
    free(tempStack); // 计数完成，释放临时栈
    // 3. 正确分配结果数组内存
    int* results = malloc(nodeCount * sizeof(int));
    if (!results) {
        *returnSize = 0;
        return NULL;
    }
    *returnSize = nodeCount;
    // 4. 再次遍历，填充结果数组
    struct TreeNode** stack = malloc(nodeCount * sizeof(struct TreeNode*)); // 需要的栈大小就是节点数
    if (!stack) {
        free(results);
        *returnSize = 0;
        return NULL;
    }
    
    top = stack;
    current = root;
    int index = 0;
    while (current != NULL || top != stack) {
        if (current != NULL) {
            results[index++] = current->val;
            *top++ = current;
            current = current->left;
        } else {
            current = *--top;
            current = current->right;
        }
    }
    free(stack);
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
    // 私有辅助递归函数
    void dfs(TreeNode* node, std::vector<int>& res) {
        if (node == nullptr) {
            return;
        }
        res.push_back(node->val); // 前序：处理根
        dfs(node->left, res);     // 然后左
        dfs(node->right, res);    // 然后右
    }
public:
    std::vector<int> preorderTraversal(TreeNode* root) {
        std::vector<int> res;
        dfs(root, res);
        return res;
    }
};
```

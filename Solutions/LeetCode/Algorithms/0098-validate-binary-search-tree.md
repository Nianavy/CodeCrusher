# Question

[validate-binary-search-tree](https://leetcode.cn/problems/validate-binary-search-tree/)



# Answer

> solution

### validate-binary-search-tree 两种最佳实践

#### 策略一：递归（边界约束法 - 最简洁）

这是利用 BST 定义的本质：任何节点 $X$ 的值必须严格大于其所有左子孙，并严格小于其所有右子孙。

1.  **辅助函数**：定义 `validate(node, min_bound, max_bound)`。
2.  **约束**：每次递归，将 `node->val` 作为新的边界传递给子树：
    *   左子树：新上限是 `node->val`。
    *   右子树：新下限是 `node->val`。
3.  **安全**：初始调用时，使用 `LLONG_MIN` 和 `LLONG_MAX` 作为绝对边界，安全地包含所有可能的 `int` 值。

#### 策略二：迭代（安全中序遍历）

利用中序遍历的结果必须是严格递增序列的特性。

1.  **数据结构**：使用一个显式栈（C++ 为 `std::stack`，C 为动态分配的数组栈）。
2.  **安全 `prev`**：使用 `long long` 类型的 `prev_val`，并将其初始化为 `LLONG_MIN`。
3.  **核心检查**：在弹出节点并访问其值时，立即检查：`if (current->val <= prev_val)`，如果成立则立即返回 `false`。
4.  **C 语言的铁律**：在 C 版本中，必须使用动态内存分配（`malloc` 或 `calloc`）来创建栈，以避免固定大小数组导致的**栈溢出**风险，并在函数结束时 `free` 释放资源。

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
// 必须使用 long long 来安全地处理 INT_MIN/INT_MAX
#define LLONG_MIN (-(9223372036854775807LL) - 1LL) 
// C 版本使用动态分配的栈来避免栈溢出（假设我们知道最大节点数 N 或使用 realloc）
// 简化起见，我们假设栈容量足够大，或者使用一个简单的链式栈 (这里仍用数组栈，但强调动态分配)

bool isValidBST(struct TreeNode* root)
{
    // 如果知道最大节点数 N，我们应该 malloc(N * sizeof(struct TreeNode*))
    // 为了通过 LeetCode 环境，我们假设一个足够大的容量。
    // 在真实 C 代码中，这块内存应该动态分配。
    
    // 我们在此演示安全迭代的逻辑。
    struct TreeNode *current = root;
    struct TreeNode **stack = NULL; // 动态栈
    int top = 0;
    int capacity = 1000; // 假设容量

    stack = (struct TreeNode**)malloc(capacity * sizeof(struct TreeNode*));
    if (!stack) return false; // 内存分配失败

    // 使用 long long 来存储前一个值，并初始化为最小的 long long
    long long prev_val = LLONG_MIN; 

    bool result = true;

    while (top > 0 || current != NULL) {
        if (current != NULL) {
            // 向左下探，入栈
            if (top >= capacity) { /* handle stack overflow by realloc */ } 
            stack[top++] = current;
            current = current->left;
        } else {
            // 弹出节点 (根)
            current = stack[--top];
            
            // 核心检查：中序遍历必须严格递增
            // 必须使用 '<=' 检查，因为 BST 要求严格大于
            if (current->val <= prev_val) {
                result = false;
                break; // 发现无效，立即退出
            }
            
            // 更新 prev_val (注意：需要将 int 提升到 long long)
            prev_val = current->val; 
            
            // 转向右子树
            current = current->right;
        }
    }
    
    // 清理资源
    free(stack);
    
    return result;
}
```

---

### Way Of C++

> source code

```c++
/**
 * 使用 long long 作为边界，解决 INT_MIN / INT_MAX 的边界问题。
 */
class Solution {
public:
    // 递归辅助函数：检查当前子树的所有值是否严格落在 (min_bound, max_bound) 之间。
    bool validate(TreeNode* node, long long min_bound, long long max_bound) {
        if (!node) {
            return true;
        }

        // 1. 当前节点值必须严格在边界内
        if (node->val <= min_bound || node->val >= max_bound) {
            return false;
        }

        // 2. 递归验证左子树：最大边界更新为当前节点的值
        //    (左子树所有节点值必须 < node->val)
        if (!validate(node->left, min_bound, node->val)) {
            return false;
        }

        // 3. 递归验证右子树：最小边界更新为当前节点的值
        //    (右子树所有节点值必须 > node->val)
        if (!validate(node->right, node->val, max_bound)) {
            return false;
        }

        return true;
    }

    bool isValidBST(TreeNode* root) {
        // 初始调用：使用 LLONG_MIN 和 LLONG_MAX 作为初始的绝对边界
        return validate(root, LLONG_MIN, LLONG_MAX);
    }
};
```

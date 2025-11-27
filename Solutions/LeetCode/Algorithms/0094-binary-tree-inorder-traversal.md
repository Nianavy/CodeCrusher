# Question

[binary-tree-inorder-traversal](https://leetcode.cn/problems/binary-tree-inorder-traversal/)



# Answer

> solution

### 中序遍历策略

#### 策略一：递归（适用于所有语言）

1.  **定义**：一个递归辅助函数 `dfs(root)`。
2.  **流程**：
    *   Base Case：如果 `root` 为空，返回。
    *   步骤 1：调用 `dfs(root->left)`。
    *   步骤 2：记录 `root->val`。
    *   步骤 3：调用 `dfs(root->right)`。

#### 策略二：迭代（显式栈模拟）

1.  **数据结构**：使用一个栈（C++ 为 `std::stack`，C 为手动实现的动态数组栈）。
2.  **流程**：
    *   使用 `current` 指针指向当前节点。
    *   **Phase 1 (下探)**：当 `current` 不为空时，将 `current` 推入栈中，并设置 `current = current->left`。
    *   **Phase 2 (访问)**：当 `current` 为空时，从栈中弹出节点，访问其值（左子树访问完毕，访问根）。
    *   **Phase 3 (右转)**：设置 `current = popped_node->right`，转向右子树，重复 Phase 1。
    *   循环终止条件：`current` 为空**且**栈为空。

#### C 语言内存管理总结

C 版本的核心挑战不是遍历本身，而是**内存安全**。我们通过以下方式确保实用主义和安全：

*   **精确计数**：在任何操作之前，通过 `count_nodes` 辅助函数精确计算树的节点总数 $N$。
*   **精确分配**：`malloc(N * sizeof(int))`。
*   **手动栈管理**：在迭代版本中，栈本身也需要手动 `calloc` 分配，并在函数结束前 `free` 释放。

### Way Of C

> source code

```c
// recursive
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
// 辅助函数 1：计算节点总数
static void count_nodes(struct TreeNode *root, int *count) {
    if (root == NULL) {
        return;
    }
    (*count)++;
    count_nodes(root->left, count);
    count_nodes(root->right, count);
}

// 辅助函数 2：执行中序遍历并填充结果
static void traverse_and_fill(struct TreeNode *node, int *result, int *current_index) {
    if (node == NULL) {
        return;
    }

    // 左
    traverse_and_fill(node->left, result, current_index);
    
    // 根
    result[*current_index] = node->val;
    (*current_index)++;
    
    // 右
    traverse_and_fill(node->right, result, current_index);
}

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* inorderTraversal(struct TreeNode* root, int* returnSize) {
    if (root == NULL) {
        *returnSize = 0;
        return NULL;
    }
    
    // 1. 第一次遍历：计算节点总数，确定精确的内存需求
    int total_count = 0;
    count_nodes(root, &total_count);
    
    *returnSize = total_count;

    // 2. 精确分配内存。绝不预估，绝不浪费。
    int *result = (int*)malloc(total_count * sizeof(int));
    if (result == NULL) {
        *returnSize = 0;
        return NULL; // 内存分配失败
    }
    
    // 3. 第二次遍历：填充数组
    int current_index = 0;
    traverse_and_fill(root, result, &current_index);
    
    return result;
}

// Iterator
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */

// --- 辅助函数原型声明 ---
// 声明是必须的，以解决隐式声明错误。
static void count_nodes(struct TreeNode *root, int *count);

// --- 栈结构和操作原型 ---
struct TreeNodeStack {
    struct TreeNode **elements;
    int capacity;
    int top;
};
static void push(struct TreeNodeStack *stk, struct TreeNode *node);
static struct TreeNode* pop(struct TreeNodeStack *stk);


// --- 主函数实现 ---

/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* inorderTraversal(struct TreeNode* root, int* returnSize) {
    if (root == NULL) {
        *returnSize = 0;
        return NULL;
    }

    // 1. 第一次遍历：计算节点总数
    int total_count = 0;
    count_nodes(root, &total_count); 
    
    *returnSize = total_count;

    // 2. 精确分配结果数组
    int *result = (int*)malloc(total_count * sizeof(int));
    if (result == NULL) {
        *returnSize = 0;
        return NULL;
    }
    
    // 3. 分配栈所需空间 (栈最大容量等于节点总数)
    struct TreeNodeStack stack;
    stack.capacity = total_count;
    stack.top = 0;
    // 使用 calloc 初始化栈指针
    stack.elements = (struct TreeNode**)calloc(total_count, sizeof(struct TreeNode*));
    if (stack.elements == NULL) {
        free(result);
        *returnSize = 0;
        return NULL;
    }
    
    struct TreeNode *current = root;
    int result_index = 0;

    // 4. 迭代遍历
    while (current || stack.top > 0) {
        // 向左下探，入栈
        while (current) {
            push(&stack, current);
            current = current->left;
        }

        // 根
        current = pop(&stack);
        result[result_index++] = current->val;

        // 向右
        current = current->right;
    }

    // 5. 释放手动分配的栈内存
    free(stack.elements);
    
    return result;
}

// --- 辅助函数实现 ---

// 辅助函数：计算节点总数
static void count_nodes(struct TreeNode *root, int *count) {
    if (root == NULL) {
        return;
    }
    (*count)++;
    count_nodes(root->left, count);
    count_nodes(root->right, count);
}

// 栈操作：入栈
static void push(struct TreeNodeStack *stk, struct TreeNode *node) {
    // 假设 total_count >= 实际节点数，因此不需要 realloc
    if (stk->top >= stk->capacity) {
        return; 
    }
    stk->elements[stk->top++] = node;
}

// 栈操作：出栈
static struct TreeNode* pop(struct TreeNodeStack *stk) {
    if (stk->top == 0) return NULL;
    return stk->elements[--stk->top];
}
```

---

### Way Of C++

> source code

```c++
// recursive
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
    std::vector<int> ans;

    void dfs(TreeNode* root) {
        if (!root) return;
        
        // 左
        dfs(root->left); 
        
        // 根
        ans.push_back(root->val); 
        
        // 右
        dfs(root->right);
    }
public:
    std::vector<int> inorderTraversal(TreeNode* root) {
        ans.clear();
        dfs(root);
        return ans;
    }
};

// Iterator
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
    std::vector<int> inorderTraversal(TreeNode* root) {
        std::vector<int> res;
        std::stack<TreeNode*> stk;

        // 只要当前节点不为空，或者栈中还有未处理的父节点，就继续循环
        while (root || !stk.empty()) {
            // 1. 一直向左下探，并将所有路径上的父节点入栈
            while (root) {
                stk.push(root);
                root = root->left;
            }

            // 2. 根：左子树访问完毕，处理当前栈顶元素（即根节点）
            root = stk.top();
            stk.pop();
            res.push_back(root->val);

            // 3. 右：转向右子树，重复步骤 1
            root = root->right;
        }

        return res;
    }
};
```

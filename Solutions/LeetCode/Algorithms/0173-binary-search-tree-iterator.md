# Question

[binary-search-tree-iterator](https://leetcode.cn/problems/binary-search-tree-iterator/)



# Answer

> solution

C++ 版本是一个 “态” 迭代器。它用一个显式栈（std::stack<TreeNode*>）来 惰性地模拟 中序遍历的递归调用栈。只在需要时（调用 next()）处理右子树，空间复杂度 O(H)（H为树高），启动成本很低。本质上是 对树结构进行实时导航。

C 版本是一个 “数组” 迭代器。它先通过一次性递归遍历，把 所有节点值都拷贝到静态数组 里，生成一个排序后的值表。后续操作只是对这个数组进行索引。空间复杂度 O(N)，初始化成本高。本质上是 预先计算并存储了排序结果。

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



#define MAX_NODES 10000
typedef struct {
    struct TreeNode *stack[MAX_NODES]; // 显式栈
    int top;                           // 栈顶指针（索引）
} BSTIterator;
BSTIterator* bSTIteratorCreate(struct TreeNode* root) {
    // 在C中，我们通常在堆上分配这个迭代器对象，以模拟C++对象的生命周期。
    BSTIterator *iter = malloc(sizeof(BSTIterator));
    if (!iter) {
        return NULL; // 处理malloc失败，这是C的责任
    }
    iter->top = -1; // 初始化为空栈
    // 构造器的核心逻辑：将root和所有左孩子压栈
    while (root) {
        // 检查栈是否溢出（在生产代码中必须检查）
        if (iter->top >= MAX_NODES - 1) {
            // 错误处理：可以释放iter并返回NULL，或者设置一个错误标志。
            // 这里为了简单，我们假设不会发生。但这是设计缺陷。
            free(iter);
            return NULL;
        }
        iter->stack[++iter->top] = root;
        root = root->left;
    }
    return iter;
}
int bSTIteratorNext(BSTIterator* obj) {
    // 有下一个吗？hasNext会检查，但我们这里必须假设调用者已经检查过。
    // 这是API设计的一部分，强迫调用者使用hasNext，或者由next内部处理。
    // 我们选择让next内部处理，以模仿C++的默认行为（在空栈时调用next是UB，但这里我们让它崩溃或返回一个值）。
    // 为了让代码更健壮，我们检查一下。
    if (obj == NULL || obj->top < 0) {
        return 0; // 这是一个糟糕的API设计，但在C中很常见。更好的做法是返回错误码。
    }
    // 弹出元素
    struct TreeNode *node = obj->stack[obj->top--];
    int val = node->val;
    
    // 处理右子树：将右子节点和所有左孩子压栈
    node = node->right;
    while (node) {
        if (obj->top >= MAX_NODES - 1) {
            // 栈溢出，这是一个致命问题。我们停止并返回已弹出的值。
            // 在真正的内核代码中，这会导致系统不稳定，必须避免。
            // 这里是演示，但我们必须标记它。
            break;
        }
        obj->stack[++obj->top] = node;
        node = node->left;
    }
    return val;
}
bool bSTIteratorHasNext(BSTIterator* obj) {
    if (obj == NULL) {
        return false;
    }
    // 直接、清晰地检查栈状态
    return (obj->top >= 0);
}
void bSTIteratorFree(BSTIterator* obj) {
    // 清理迭代器对象本身。
    // 注意：这个迭代器不拥有树节点，只拥有指针。所以不需要释放树。
    free(obj);
}

/**
 * Your BSTIterator struct will be instantiated and called as such:
 * BSTIterator* obj = bSTIteratorCreate(root);
 * int param_1 = bSTIteratorNext(obj);
 
 * bool param_2 = bSTIteratorHasNext(obj);
 
 * bSTIteratorFree(obj);
*/
```

---

### Way Of C++

> source code

```c++
class BSTIterator {
public:
    stack<TreeNode*> stk;

    BSTIterator(TreeNode* root) {
        while (root) {
            stk.push(root);
            root = root->left;
        }
    }

    /** @return the next smallest number */
    int next() {
        auto root = stk.top();
        stk.pop();
        int val = root->val;
        root = root->right;
        while (root) {
            stk.push(root);
            root = root->left;
        }
        return val;
    }

    /** @return whether we have a next smallest number */
    bool hasNext() {
        return stk.size();
    }
};
```

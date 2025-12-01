# Question

[recover-binary-search-tree](https://leetcode.cn/problems/recover-binary-search-tree/)



# Answer

> solution

### O(1) 空间迭代 (Morris 遍历)

虽然递归更简洁，但 **Morris 遍历**提供了 $O(1)$ 额外空间复杂度的优势，这符合对效率和资源控制的追求。

1.  **状态存储**：使用少数几个指针 (`first`, `second`, `last`) 存储状态，并利用树结构本身进行回溯。
2.  **遍历**：执行 Morris 中序遍历。
3.  **核心逻辑**：在每次中序访问节点 `current` 时，检查 `if (last->val > current->val)`：
    *   **如果 `first` 为空**：这是第一次下降。将 `first` 记录为 `last` ($A$)，将 `second` 记录为 `current` ($B$)。
    *   **如果 `first` 不为空**：这是第二次下降。只需将 `second` 更新为 `current` ($D$)。
4.  **交换**：遍历结束后，交换 `first` 和 `second` 节点的值。

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
// 使用结构体存储状态，避免传递过多的双重指针
struct State {
    struct TreeNode *prev;  // 中序前驱
    struct TreeNode *p1;    // 第一个错位节点 (A)
    struct TreeNode *p2;    // 第二个错位节点 (B 或 D)
    int wrong_count;        // 发现的错位次数 (1或2)
};

static void dfs_c(struct TreeNode *node, struct State *state)
{
    if (node == NULL || state->wrong_count == 2) {
        return;
    }

    dfs_c(node->left, state);

    // 核心检查：中序遍历中的递减点 (prev > current)
    if (state->prev != NULL && state->prev->val > node->val) {
        
        state->wrong_count++;
        
        if (state->wrong_count == 1) {
            // 第一次错位：记录第一个错位点 (A) 和当前点 (B)
            state->p1 = state->prev;
            state->p2 = node;
        } else if (state->wrong_count == 2) {
            // 第二次错位：只更新第二个错位点为当前点 (D)
            state->p2 = node;
        }
    }
    
    // 更新前驱
    state->prev = node;

    dfs_c(node->right, state);
}

void recoverTree(struct TreeNode* root)
{
    // 初始化状态结构体
    struct State state = {
        .prev = NULL,
        .p1 = NULL,
        .p2 = NULL,
        .wrong_count = 0
    };
    
    dfs_c(root, &state);

    // 交换值
    if (state.p1 && state.p2) {
        int tmp = state.p1->val;
        state.p1->val = state.p2->val;
        state.p2->val = tmp;
    }
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
    void recoverTree(TreeNode* root) {
        TreeNode *first = nullptr;   // 记录第一个错位点 (A)
        TreeNode *second = nullptr;  // 记录第二个错位点 (D 或 B)
        TreeNode *last = nullptr;    // 记录中序遍历中的前一个节点 (prev)
        
        TreeNode *current = root;

        // 使用 Morris 遍历实现 O(1) 空间的中序遍历
        while (current) {
            if (!current->left) {
                // 1. 无左子树：访问当前节点
                
                // 检查递减：last 相当于 prev
                if (last && last->val > current->val) {
                    if (!first) {
                        // 第一次发现错位: 记录 A 和 B
                        first = last;
                        second = current; // 初始假设 B 是第二个错位点
                    } else {
                        // 第二次发现错位: 更新第二个错位点为 D
                        second = current;
                    }
                }
                last = current; // 更新前驱
                current = current->right; // 转向右子树
            } else {
                // 2. 有左子树：寻找中序前驱
                TreeNode *predecessor = current->left;
                while (predecessor->right && predecessor->right != current) {
                    predecessor = predecessor->right;
                }

                if (!predecessor->right) {
                    // 建立临时链接: 向左走，并将前驱的右指针指向当前节点
                    predecessor->right = current;
                    current = current->left;
                } else {
                    // 移除临时链接 (第二次访问): 访问当前节点 (根)
                    predecessor->right = nullptr;
                    
                    // 检查递减
                    if (last && last->val > current->val) {
                        if (!first) {
                            first = last;
                            second = current;
                        } else {
                            second = current;
                        }
                    }
                    last = current; // 更新前驱
                    current = current->right; // 转向右子树
                }
            }
        }
        
        // 交换值
        if (first && second) {
            std::swap(first->val, second->val);
        }
    }
};
```

# Question

[populating-next-right-pointers-in-each-node](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)



# Answer

> solution

本题要求将一棵完美二叉树的每一层节点从左到右用 next 指针连接起来。关键在于利用完美二叉树的结构特性：每一层都是满的，且每个非叶节点都有左右两个子节点。

我们不需要使用队列做层序遍历，也不需要递归。只需从根开始，逐层向下处理。对于当前层，由于 next 指针已经连好（初始时只有根，视为已连好），我们可以像遍历链表一样横向走一遍，同时为下一层建立 next 链接：每个节点的左子指向右子，右子则指向其右侧邻居的左子（如果存在）。

处理完一层后，直接跳到下一层最左边的节点继续。整个过程只用几个指针变量，空间复杂度 O(1)，时间上每个节点访问一次，复杂度 O(n)。

代码因此变得极简：一个外层循环控制层数，一个内层循环横向连接，没有多余判断，没有特殊情况——这就是好品味。
```
  算法步骤
从根节点开始，设 level = root 表示当前处理层的最左节点。
当 level->left 存在时（说明还有下一层）：
遍历当前层所有节点（通过 p = p->next）；
对每个节点 p：
连接 p->left → p->right；
若 p 有右侧邻居（p->next 非空），则连接 p->right → p->next->left。
将 level 下移至 level->left，进入下一层。
返回原 root。
```

### Way Of C

> source code

```c
/**
 * Definition for a Node.
 * struct Node {
 *     int val;
 *     struct Node *left;
 *     struct Node *right;
 *     struct Node *next;
 * };
 */

/**
 * 将完美二叉树每一层的节点用 next 指针从左到右连接。
 * 利用已建立的上层 next 链，逐层向下构建下一层的 next 链。
 * 空间复杂度 O(1)，无需队列或递归。
 */
struct Node* connect(struct Node* root) {
    if (!root) return root;  // 空树直接返回

    struct Node* level = root;  // 当前正在处理的层的最左节点
    while (level->left) {       // 只要还有下一层（完美二叉树保证 left 存在即整层存在）
        // 遍历当前层的所有节点（通过 next 链）
        for (struct Node* p = level; p != NULL; p = p->next) {
            p->left->next = p->right;          // 左子节点 → 右子节点
            if (p->next) {
                p->right->next = p->next->left; // 右子节点 → 下一节点的左子节点
            }
        }
        level = level->left;  // 下移至下一层的最左节点
    }
    return root;
}
```

---

### Way Of C++

> source code

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

/**
 * 将完美二叉树每一层的节点用 next 指针从左到右连接。
 * 利用父层已建好的 next 链，隐式完成层序遍历，空间 O(1)。
 */
class Solution {
public:
    Node* connect(Node* root) {
        if (!root) return root;  // 空树直接返回

        Node* level = root;  // 当前层的最左节点
        while (level->left) { // 完美二叉树：只要 left 存在，整层就完整
            // 沿当前层的 next 链横向遍历
            for (Node* p = level; p != nullptr; p = p->next) {
                p->left->next = p->right;          // 同父左右相连
                if (p->next) {
                    p->right->next = p->next->left; // 跨父右→左相连
                }
            }
            level = level->left;  // 下移至下一层最左
        }
        return root;
    }
};
```

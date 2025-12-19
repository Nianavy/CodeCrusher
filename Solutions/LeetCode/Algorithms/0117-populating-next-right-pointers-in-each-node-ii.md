# Question

[populating-next-right-pointers-in-each-node-ii](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/)



# Answer

> solution

解决的核心思路是：逐层遍历，动态收集下一层的所有非空子节点，并将它们串成一条链。关键在于如何在 O(1) 额外空间内完成这一过程。

具体做法是：

用一个指针 cur 表示当前正在处理的层（初始为根）。
对每一层，创建一个栈上的 dummy 节点作为下一层链表的头，配合 tail 指针尾插所有非空子节点（先左后右）。
遍历完当前层后，下一层的起点就是 dummy.next，将其赋给 cur 进入下一轮。
重复直到某层没有子节点为止。
这种方法完全利用已有的 next 指针进行横向遍历，用链表拼接技巧模拟层序遍历，无需队列、无需递归、无堆分配、空间复杂度 O(1)，同时逻辑清晰、鲁棒性强，适用于任意二叉树结构。

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

struct Node* connect(struct Node* root) {
    struct Node* cur = root;
    while (cur) {
        struct Node dummy = {0, NULL, NULL, NULL}; // 栈上 dummy
        struct Node* tail = &dummy;

        for (struct Node* p = cur; p; p = p->next) {
            if (p->left)  tail = tail->next = p->left;
            if (p->right) tail = tail->next = p->right;
        }

        cur = dummy.next;
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

class Solution {
public:
    Node* connect(Node* root) {
        Node* cur = root;
        while (cur) {
            // 使用栈上 dummy 节点，避免 new/delete
            Node dummy(0);  
            Node* tail = &dummy;

            // 遍历当前层，收集所有非空子节点
            for (Node* p = cur; p; p = p->next) {
                if (p->left)  tail = tail->next = p->left;
                if (p->right) tail = tail->next = p->right;
            }

            // 下一层的起点是 dummy.next
            cur = dummy.next;
        }
        return root;
    }
};
```

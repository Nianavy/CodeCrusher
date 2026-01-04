# Question

[copy-list-with-random-pointer](https://leetcode.cn/problems/copy-list-with-random-pointer/)



# Answer

> solution

原地插针法（Interleaving Copy），这是解决此问题的最优解。它通过巧妙利用链表的物理结构，将克隆节点、建立 random 关系和拆分链表这三个步骤融合在一起。

解决思路总结（三步走）
1. 复制节点并拼接 (Cloning & Interleaving)
遍历原链表，对于每一个节点 p：

克隆一个节点 q，值与 p 相同。
将 q 插入到 p 和 p->next 之间。
结果：p -> q -> p_next -> q_next ...
此时，原链表和新链表交织在一起。新链表的节点可以通过 p->next 访问。

2. 建立随机指针 (Setting Random Pointers)
再次遍历链表（每次跳两步）。

对于原节点 p 和它后面紧挨着的新节点 q (q = p->next)。
如果 p 有 random 指针，那么 q 的 random 应该指向 p->random 的下一个节点（即克隆出来的那个节点）。
公式：q->random = (p->random != NULL) ? p->random->next : NULL
如果 p->random 是 NULL，则 q->random 也自然是 NULL。
3. 拆分链表 (Splitting)
将交织的两个链表拆分开，还原原链表，并提取出新链表。

使用两个指针：一个指向当前处理的位置（原节点），一个用于遍历新链表。
始终维护 new_node->next 指向下一个 new_node。
同时恢复 old_node->next 指向下一个 old_node。
为什么这个方法好？
空间复杂度 O(1)：除了生成新节点所需的空间外，没有使用哈希表或额外的大数组。
时间复杂度 O(N)：仅遍历了三次链表。
逻辑严密：利用了节点的物理相邻关系来隐式存储映射关系，非常优雅。

### Way Of C

> source code

```c
/**
 * Definition for a Node.
 * struct Node {
 *     int val;
 *     struct Node *next;
 *     struct Node *random;
 * };
 */
struct Node* copyRandomList(struct Node* head) {
    if (!head) return NULL;
    struct Node *p, *new_head;
    /* 1. 插入拷贝节点: 1 -> 1' -> 2 -> 2' -> ... */
    for (p = head; p; p = p->next->next) {
        struct Node *new_node = (struct Node*)malloc(sizeof(struct Node));
        new_node->val = p->val;
        new_node->next = p->next;
        p->next = new_node;
    }
    /* 2. 设置随机指针 */
    for (p = head; p; p = p->next->next) {
        /* 这里的三元运算符和 C++ 版本逻辑一致 */
        p->next->random = (p->random) ? p->random->next : NULL;
    }
    /* 3. 拆分链表: 修复你的逻辑漏洞 */
    /* 辅助指针，dummy 节点是处理链表分裂的标准做法，避免特殊处理头节点 */
    struct Node dummy;
    struct Node *tail = &dummy; 
    
    p = head;
    while (p) {
        /*
         * 逻辑步骤：
         * a. 拿到原节点和新节点
         * b. 将新节点接到新链表的尾部
         * c. 恢复原链表的 next 指针
         * d. 移动 p 到下一个原节点
         */
        struct Node *original = p;
        struct Node *copy = p->next;
        // 链接新链表
        tail->next = copy;
        tail = copy;
        // 恢复原链表
        original->next = copy->next;
        // 移动到下一个原节点
        p = original->next;
    }
    /* 此时 copy->next 已经在上面的循环中被正确置为 NULL */
    return dummy.next;
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
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/
class Solution {
public:
    Node* copyRandomList(Node* head) {
        for (auto p = head; p; p = p->next->next) {  // 复制一个小弟
            auto q = new Node(p->val);
            q->next = p->next;
            p->next = q;
        }

        // 复制random指针
        for (auto p = head; p; p = p->next->next)
            if (p->random)
                p->next->random = p->random->next;

        // 拆分两个链表
        auto dummy = new Node(-1), cur = dummy;
        for (auto p = head; p; p = p->next) {
            auto q = p->next;
            cur = cur->next = q;
            p->next = q->next;
        }
        return dummy->next;
    }
};
```

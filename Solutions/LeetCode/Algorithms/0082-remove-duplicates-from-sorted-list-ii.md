# Question

[remove-duplicates-from-sorted-list-ii](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)



# Answer

> solution

链表去重（删除所有重复项）

这个问题，如果简化到本质，就是如何**用一个前置指针（`prev`）高效地绕过一个或多个连续重复的节点序列**。

#### 核心哲学：消除特殊情况，实用主义至上。

1.  **哑节点 (Dummy Node) 消除头部特殊情况：**
    *   **作用：** 避免在处理 `head` 本身需要被删除时进行特殊判断。`prev` 指针永远从 `dummy` 开始，简化了代码。
    *   **品味：** 这是链表操作中，解决边界问题的**良好品味**。

2.  **双指针策略 (Prev & Runner)：**
    *   **`prev` (前置指针)：** 永远指向**上一个被保留的节点**。这是我们进行连接（删除）操作的锚点。
    *   **`p` (序列起点)：** 标记当前要检查的序列的第一个节点。
    *   **`q` (查找指针/Runner)：** 从 `p` 开始，向前推进，直到找到**第一个不重复的节点**。

3.  **核心逻辑：判断与操作分离**
    *   **查找重复：** `q` 负责遍历。如果 `q->val == p->val`，就让 `q` 继续前进。
    *   **判断结果：** 检查 `p->next` 是否等于 `q`。
        *   **情况 A (`p->next == q`)：** 如果是，说明 `q` 只比 `p` 前进了一步（或者根本没前进），`p` 是一个单独节点，没有重复。
            *   **操作：** `prev` 跟着 `p` 前进 (`prev = p`)。
        *   **情况 B (`p->next != q`)：** 否则，说明 `p` 和 `q` 之间有一串重复节点。
            *   **操作：** **必须处理内存释放**。遍历 `p` 到 `q` 之间的所有节点并释放（C用 `free`，C++用 `delete`）。然后，将 `prev->next` 直接指向 `q`，从而跳过并删除整个重复序列。
            *   **关键：** 此时 `prev` **不应该**移动，它仍然指向 `q` 的前一个节点，确保下一轮循环从 `q` 开始检查。

4.  **内存管理：实用主义的铁律**
    *   **缺陷：** 仅执行 `prev->next = q` 只是“绕过”，会导致内存泄漏。
    *   **修正：** 任何删除操作，都必须在断开链表之前，用一个临时指针遍历并释放被删除的节点序列。否则，这是**垃圾代码**。

**总结：** 采用哑节点和双指针进行 O(N) 遍历。关键在于高效地利用 `prev` 来“锚定”有效节点，然后用 `q` 找到下一个有效节点，并在中间执行原子性的“释放-连接”操作。

### Way Of C

> source code

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
/**
 * 删除链表中所有重复的节点，只保留出现一次的节点。
 * 核心：使用哑节点消除头部特殊情况，并使用双指针（prev 和 runner）处理序列。
 */
struct ListNode* deleteDuplicates(struct ListNode* head) {
    if (head == NULL) {
        return NULL;
    }

    struct ListNode dummy;
    dummy.next = head;
    struct ListNode *prev = &dummy;
    struct ListNode *p = head; // p 用于查找重复序列的起点

    while (p != NULL) {
        struct ListNode *q = p; // q 用于查找重复序列的终点

        // 1. 查找重复序列的末尾
        // q 会一直前进，直到找到第一个与 p->val 不相等的节点，或到达链表末尾
        while (q != NULL && q->val == p->val) {
            q = q->next;
        }

        // 2. 判断是否发生了重复
        // 如果 q 恰好是 p 的下一个节点，说明 p 没有重复，是一个独立节点
        if (p->next == q) {
            // 没有重复，prev 简单地前进
            prev = p;
        } else {
            // 发生了重复 (p 到 q 之间的所有节点都需要被删除)
            
            // 内存释放：遍历并释放从 p 开始到 q 之前的节点
            struct ListNode *node_to_free = p;
            while (node_to_free != q) {
                struct ListNode *temp = node_to_free->next;
                free(node_to_free); // 释放内存
                node_to_free = temp;
            }
            
            // 绕过重复序列：将 prev 直接指向 q
            prev->next = q;
        }
        
        // 3. 进入下一轮循环，p 从 q 开始（跳过已处理的序列）
        p = q;
    }

    return dummy.next;
}
```

---

### Way Of C++

> source code

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        // 哑节点是消除头部特殊情况的最简单方法。
        auto dummy = new ListNode(-1);
        dummy->next = head;
        
        auto prev = dummy; // 始终指向当前处理序列的前一个有效节点
        
        while (prev->next) {
            auto p = prev->next; // 当前序列的起点
            auto q = p->next;    // 查找重复序列的游标
            
            // 1. 查找重复序列的终点 q
            while (q && q->val == p->val) {
                q = q->next;
            }
            
            // 2. 判断是否发生了重复
            if (p->next == q) {
                // 没有重复 (p 只是一个单独的节点)
                prev = prev->next;
            } else {
                // 有重复 (p 到 q 之间的所有节点都是重复的，必须删除)
                
                // 内存释放：遍历并 delete 节点
                auto node_to_delete = p;
                while (node_to_delete != q) {
                    auto next_node = node_to_delete->next;
                    delete node_to_delete;
                    node_to_delete = next_node;
                }
                
                // 绕过重复序列：将 prev 直接指向 q
                prev->next = q;
                
                // 注意：prev 不动。它仍然是 q 的前一个节点，确保下一轮循环从 q 开始检查。
            }
        }
        
        // 记住要释放创建的哑节点
        head = dummy->next;
        delete dummy;
        
        return head;
    }
};
```

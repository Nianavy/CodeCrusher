# Question

[remove-duplicates-from-sorted-list](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)



# Answer

> solution

有序链表去重（保留首个实例）

这是链表操作中最基础和最简洁的问题之一，不应该被复杂化。

#### 核心哲学：极简设计，内存安全。

1.  **拒绝冗余的哑节点：**
    *   **问题性质：** 这个任务是保留链表的头节点 (`head`)。
    *   **原则：** 如果不需要修改 `head` 指针，就不应该引入哑节点。你提交的 C 版本引入的 `dummy` 和 `INT_MIN` 是**过度工程**和**糟糕的品味**。

2.  **极简双指针策略：**
    *   我们只需要两个指针来实现 O(N) 遍历：
        *   `cur`：**保留指针**。它总是指向链表中上一个被保留的有效节点。
        *   `p`：**查找指针 (Runner)**。它遍历链表，寻找下一个不重复的节点。

3.  **核心逻辑：三段式判断与操作**

    *   **如果 `p->val == cur->val` (重复)：**
        *   **操作：** 必须释放 `p` 所在的重复节点。将 `p` 推进到下一个节点。`cur` **保持不动**。
        *   **目标：** 持续跳过并释放所有重复项，直到找到新的值。

    *   **如果 `p->val != cur->val` (不重复)：**
        *   **操作：** 利用最简洁的写法将 `cur` 的 `next` 指向 `p`，同时将 `cur` 前进到 `p` 的位置。
        *   **简洁写法：** `cur = cur->next = p;`

    *   **循环结束：**
        *   **操作：** 确保链表的末尾被正确终止，`cur->next = NULL` (或 `nullptr`)。

4.  **实用主义的铁律：内存管理**
    *   **强制要求：** 任何链表删除操作，都必须伴随着 `free` (C) 或 `delete` (C++)。
    *   **原因：** 如果仅仅跳过重复节点而不释放内存，这是**致命的内存泄漏**，在生产环境中不可接受。

**总结：** 简洁地使用双指针，以 `cur = cur->next = p` 优雅地处理非重复情况，同时确保在重复时释放内存。这是 O(N) 时间和 O(1) 空间的最佳实践。

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
struct ListNode* deleteDuplicates(struct ListNode* head) {
    if (head == NULL) {
        return NULL;
    }

    struct ListNode *cur = head;
    struct ListNode *p = head->next;

    while (p != NULL) {
        if (p->val != cur->val) {
            // 情况A：找到不重复的新节点。
            // cur 连接到 p，然后 cur 前进到 p。
            cur = cur->next = p;
        } else {
            // 情况B：重复节点。必须释放内存。
            struct ListNode *node_to_free = p;
            
            // p 继续前进。
            p = p->next;
            
            // 释放被跳过的节点。
            free(node_to_free);
            
            continue;
        }
        
        // 如果前面没有执行 continue (即 cur 前进了)，p 也前进。
        p = p->next;
    }

    // 确保链表正确终止。
    cur->next = NULL;
    
    return head;
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
        if (!head) {
            return head;
        }

        // cur 始终指向当前被保留的有效节点。
        auto cur = head;
        
        // p 充当 runner，遍历后续节点。
        auto p = head->next;
        
        while (p) {
            if (p->val != cur->val) {
                // 情况A：找到一个不重复的新节点。
                // 1. 将 cur 连接到 p。
                // 2. 将 cur 前进到 p。
                cur = cur->next = p;
            } else {
                // 情况B：找到一个重复的节点。
                // 必须释放内存。
                auto node_to_delete = p;
                
                // p 继续向前，找到下一个节点。
                p = p->next;
                
                // 释放被跳过的重复节点。
                delete node_to_delete;
                
                // 注意：cur 保持不动，因为它仍然是上一个被保留的节点。
                continue;
            }
            // 如果 p->val != cur->val，那么 cur 已经前进到了 p。
            // 此时 p 也应该继续前进到下一个节点。
            p = p->next;
        }

        // 循环结束后，确保链表正确终止。
        cur->next = nullptr; 
        
        return head;
    }
};
```

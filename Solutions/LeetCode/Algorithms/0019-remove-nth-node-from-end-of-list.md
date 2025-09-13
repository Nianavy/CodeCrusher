# Question

[remove-nth-node-from-end-of-list](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)



# Answer

> solution

解决这类链表删除问题的核心思路，是利用**虚拟头节点**简化边缘情况，并采用**双指针（快慢指针）**实现单次遍历定位。

1.  **处理无效的 `n` 值。**
    *   **原则：** 如果 `n <= 0`，则这是一个无效的操作（删除倒数第 0 或负数个节点没有意义）。在这种情况下，应直接返回原始链表头，不进行任何删除。这是防御性编程的一部分。

2.  **引入虚拟头节点 (Dummy Node)。**
    *   **目的：** 这是处理链表头节点删除问题的“银弹”。虚拟头节点是一个不属于实际链表，但指向链表头部的临时节点。
        *   它确保了即使原始头节点被删除，`prev->next` (即 `slow->next`) 也总是一个有效操作，`prev` 永远不会是 `NULL`。
        *   最终结果总是 `dummy->next`，简化了返回逻辑。
    *   **实现：** 创建一个 `dummy` 节点，将其 `next` 指向原始 `head`。C++ 可以是 `ListNode dummy(0);` (栈上)，C 可以是 `struct ListNode dummy;` (栈上)。

3.  **使用双指针 (快慢指针)。**
    *   **原理：** 设定两个指针 `fast` 和 `slow`。让 `fast` 先行 `n` 步。然后，`fast` 和 `slow` 同时前进，直到 `fast` 到达链表的末尾。
    *   **结果：** 当 `fast` 到达链表末尾（即 `fast->next` 为 `NULL`）时，`slow` 指针自然会停在要删除节点的前一个位置。
    *   **实现：**
        *   初始化：`fast = &dummy;`，`slow = &dummy;`
        *   快指针先行：`for (int i = 0; i < n; ++i) { fast = fast->next; }`
            *   **鲁棒性：** 在此循环中，每次移动 `fast` 前，检查 `fast == NULL`。如果 `fast` 变为 `NULL`，说明 `n` 太大，超出了链表的实际长度。此时应返回原始 `head`（或 `dummy.next`），不进行删除。
        *   同步前进：`while (fast->next != NULL) { fast = fast->next; slow = slow->next; }`

4.  **执行删除操作。**
    *   **定位：** `slow` 指针现在指向了要删除节点的前一个节点。因此，`slow->next` 就是要删除的目标节点。
    *   **重链：** 将 `slow->next` 指向 `slow->next->next`。这有效地将目标节点从链表中“跳过”或“移除”。
    *   **内存管理：**
        *   C 语言：在重链后，**必须 `free(node_to_delete)`** 释放被删除节点的内存，以防止内存泄漏。
        *   C++：如果节点是通过 `new` 创建的，则**应 `delete node_to_delete;`**。但在 LeetCode 平台，通常不强制要求。

5.  **返回结果。**
    *   始终返回 `dummy.next` 作为新的链表头。这统一了逻辑，无需额外判断头节点是否被删除的情况。

**总结来说，核心思路是：用一个虚拟头节点统一删除逻辑，用快慢双指针一次遍历定位到目标节点的前一个节点，然后执行标准的链表删除和内存管理，并最终返回虚拟头节点的下一个节点。**

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
struct ListNode* removeNthFromEnd(struct ListNode* head, int n) {
    if (n <= 0) return head;
    struct ListNode dummy;
    dummy.val = 0;
    dummy.next = head;

    struct ListNode *fast = &dummy;
    struct ListNode *slow = &dummy;

    for (int i = 0; i < n; ++i) {
        if (fast == NULL) return head;
        fast = fast->next;
    }

    if (fast == NULL) return head;

    while (fast->next != NULL) {
        fast = fast->next;
        slow = slow->next;
    }

    struct ListNode *node_to_delete = slow->next;
    slow->next = node_to_delete->next;

    free(node_to_delete);

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if (n <= 0) return head;
        ListNode dummy(0);
        dummy.next = head;
        ListNode *fast = &dummy;
        ListNode *slow = &dummy;

        for (int i = 0; i < n; ++i) {
            if (fast == nullptr) return head;
            fast = fast->next;
        }

        if (fast == nullptr) return head;

        while (fast->next != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }

        ListNode *node_to_delete = slow->next;
        slow->next = node_to_delete->next;

        return dummy.next;
    }
};
```

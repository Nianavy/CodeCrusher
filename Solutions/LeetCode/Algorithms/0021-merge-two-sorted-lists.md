# Question

[merge-two-sorted-lists](https://leetcode.cn/problems/merge-two-sorted-lists/)



# Answer

> solution

核心思路：
1.  **虚拟头节点 (Dummy Node)**：
    *   **作用**：为了简化代码逻辑，尤其是在处理链表头部的特殊情况时。如果没有虚拟头节点，你需要在合并的第一个元素是来自 `list1` 还是 `list2` 的时候做特殊判断，并且返回时也需要一个额外的变量来跟踪真正的头部。
    *   **实现**：创建一个临时的 `ListNode` 对象（在 C 中直接是 `struct ListNode dummy;`，在 C++ 中是 `ListNode dummy;`），它的 `next` 指针最初指向合并后的链表的第一个节点。最终返回 `dummy.next`。
    *   **Linus 哲学**：这是“好品味”的体现。它通过一个巧妙的预处理，消除了所有需要特殊处理的边界情况（比如空链表输入、或者第一个节点来自哪个链表）。“好代码没有特殊情况。”

2.  **“尾巴”指针 (Tail Pointer)**：
    *   **作用**：用于追踪当前合并后链表的末尾。每当找到一个要添加到合并链表中的节点时，就将 `tail->next` 指向这个节点，然后将 `tail` 本身更新为这个新添加的节点。
    *   **实现**：`ListNode* tail = &dummy;` 初始化。循环中 `tail->next = chosen_node; tail = tail->next;`。
    *   **Linus 哲学**：简洁、高效的指针操作，避免了不必要的查找或遍历。

3.  **循环比较与连接**：
    *   **作用**：遍历两个输入链表，逐个比较当前节点的值，将值较小的节点连接到合并链表的末尾。
    *   **实现**：`while (list1 != nullptr && list2 != nullptr)` 循环，内部 `if (list1->val < list2->val) { ... } else { ... }` 判断。
    *   **Linus 哲学**：直接了当，避免复杂逻辑。

4.  **处理剩余部分**：
    *   **作用**：当一个链表遍历完后，另一个链表可能还有剩余节点。这些剩余节点必然都比已合并的节点大（或相等，取决于比较符）。
    *   **实现**：循环结束后，直接将 `tail->next` 指向未遍历完的那个链表 (`tail->next = (list1 != nullptr) ? list1 : list2;`)。
    *   **Linus 哲学**：再次利用简单判断消除特殊情况，避免额外的循环。

5.  **内存管理 (C++ 裸指针环境下的关键)**：
    *   **作用**：在 C++ 中使用裸指针时，确保没有内存泄漏是至关重要的。
    *   **实现**：**将虚拟头节点 `dummy` 在栈上创建 (`ListNode dummy;`)，而不是堆上 (`new ListNode()`)**。这样就完全规避了 `dummy` 节点的内存泄漏问题。对于输入链表的节点，此算法只是重排了它们的 `next` 指针，不创建也不删除任何节点，因此没有额外的内存分配/释放责任。调用者负责管理输入链表的内存。
    *   **Linus 哲学**：实用主义，解决实际问题（内存泄漏）。“如果你使用了 `new`，但却没有对应的 `delete`，你就是在制造垃圾。”

**总结起来，核心思路就是：通过一个栈上的虚拟头节点和尾指针，以O(1)的额外空间复杂度和O(N+M)的时间复杂度，将两个有序链表巧妙地重新连接成一个有序链表，同时避免了不必要的内存分配和复杂的边界条件判断。**

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
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode dummy = { .val = 0, .next = NULL };
    struct ListNode *tail = &dummy;

    while (l1 != NULL && l2 != NULL) {
        if (l1->val <= l2->val) {
            tail->next = l1;
            l1 = l1->next;
        } else {
            tail->next = l2;
            l2 = l2->next;
        }
        tail = tail->next;
    }
    tail->next = (l1 != NULL) ? l1 : l2;
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
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode dummy;
        ListNode *tail = &dummy;

        while (list1 != nullptr && list2 != nullptr) {
            if (list1->val < list2->val) {
                tail->next = list1;
                list1 = list1->next;
            } else {
                tail->next = list2;
                list2 = list2->next;
            }
            tail = tail->next;
        }
        if (list1 != nullptr) {
            tail->next = list1;
        } else {
            tail->next = list2;
        }
        return dummy.next;
    }
};
```

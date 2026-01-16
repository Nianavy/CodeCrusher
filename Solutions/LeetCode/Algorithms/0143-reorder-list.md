# Question

[reorder-list](https://leetcode.cn/problems/reorder-list/)



# Answer

> solution

## **解决思路总结**

解决链表重排问题，核心是 **“分而治之”** 。因为单向链表无法向后操作，所以必须通过将链表**切开、反转、再重新拼接**来达到目的。

### **三步走：**

1.  **找中点**：
    用快慢指针法（慢指针一次一步，快指针一次两步），当快指针走到尽头时，慢指针恰好停在链表的中点。

2.  **反转后半段**：
    将链表从**慢指针的位置之后**切开，得到后半部分列表，然后将这个后半部分链表整体反转。

3.  **交叉合并**：
    将原链表的前半部分与反转后的后半部分交替连接起来。由于后半部分反转后，其尾部变成了新的表头，连接方式自然是从两端向中间汇聚。

### **关键优势：**

- **无需计算长度**：快慢指针自动处理长度问题。
- **优雅处理奇偶**：合并时，只需要遍历后半部分链表的长度，前半部分剩余节点会被后半部分的最后一个节点自然覆盖（如果处理顺序正确的话）。实际上，因为前半部分总是比后半部分长或等长，合并循环以**后半部分结束**为条件，前半部分多余的节点（仅在奇数长度时会出现一个）会自然地成为最后一个节点（即原链表中间节点），无需特殊处理。
- **清晰直接**：每个步骤只做一件事，没有复杂的条件判断或边界状处理。这正是“好品味”代码的体现。

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
// 辅助函数：反转链表，返回新的头节点
static struct ListNode* reverseList(struct ListNode* head) {
    struct ListNode *prev = NULL;
    struct ListNode *curr = head;
    while (curr) {
        struct ListNode *next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}
// 主函数：重排链表
void reorderList(struct ListNode* head) {
    if (head == NULL || head->next == NULL) {
        return;
    }
    // 1. 使用快慢指针找到中点（slow 指向第一个下半部分的节点）
    //    - 对于偶数长度， slow 在上半部分的末尾。
    //    - 对于奇数长度， slow 在中间节点。
    struct ListNode *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    // 2. 分割链表并反转后半部分
    //    - 将前半部分的尾部节点的next置为NULL
    struct ListNode *secondHalf = slow->next;
    slow->next = NULL;
    //    - 反转后半部分
    secondHalf = reverseList(secondHalf);
    // 3. 合并两个链表
    //    此时，secondHalf 的长度 <= head 的长度
    struct ListNode *p1 = head, *p2 = secondHalf;
    while (p2) {
        struct ListNode *t1 = p1->next;
        struct ListNode *t2 = p2->next;
        p1->next = p2;
        p2->next = t1;
        p1 = t1;
        p2 = t2;
    }
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
    // 辅助函数：反转链表
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
    void reorderList(ListNode* head) {
        if (!head || !head->next) {
            return;
        }
        // 1. 找中点
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        // 2. 分割并反转后半部分
        ListNode* secondHalf = slow->next;
        slow->next = nullptr; // 清晰地断开前半部分
        secondHalf = reverseList(secondHalf);
        // 3. 合并
        ListNode* p1 = head;
        ListNode* p2 = secondHalf;
        while (p2) {
            ListNode* t1 = p1->next;
            ListNode* t2 = p2->next;
            p1->next = p2;
            p2->next = t1;
            p1 = t1;
            p2 = t2;
        }
    }
};
```

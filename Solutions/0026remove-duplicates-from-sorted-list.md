https://leetcode.cn/problems/remove-duplicates-from-sorted-list

### Think
```txt
移除已排序链表中重复的元素，只保留唯一的元素。它使用迭代的方式遍历链表并进行操作。

以下是代码的具体操作：

初始化: ListNode *cur = head;  将 cur 指针初始化为链表头 head。

循环条件: while (cur && cur->next)  循环继续的条件是 cur 指针不为空，并且 cur 的下一个节点也不为空。这意味着循环会一直进行到链表的倒数第二个节点（如果有的话）。

判断重复: if (cur->val == cur->next->val)  比较当前节点 cur 的值和下一个节点 cur->next 的值。如果值相同，则说明存在重复元素。

删除重复节点:

ListNode* temp = cur->next; 将 cur->next 指针临时存储在 temp 中，以便后续释放内存。
cur->next = cur->next->next; 将 cur->next 指针指向 cur->next->next，也就是跳过了重复的节点，使 cur 的下一个节点成为原先重复节点的下一个节点。
delete temp; 释放被删除节点的内存，避免内存泄漏。
移动指针: else { cur = cur->next; }  如果当前节点和下一个节点的值不同，则将 cur 指针移动到下一个节点，继续遍历。

返回链表头: return head;  循环结束后，返回修改后的链表的头节点 head。
```

### Ways
By C++:
```cpp
class Solution {
public:
    ListNode *deleteDuplicates(ListNode *head) {
        ListNode *cur = head;
        while (cur && cur->next) {
            if (cur->val == cur->next->val) {
                ListNode *temp = cur->next;
                cur->next = cur->next->next;
                delete temp; 
            } else {
                cur = cur->next;
            }
        }
        return head;
    }
};
```

https://leetcode.cn/problems/reverse-linked-list

### Think
```txt
使用迭代的方法即可.
反转逻辑:
使用三个指针 prev、curr 和 next 来进行链表的反转。
prev 初始化为 nullptr，因为它将指向反转后链表的最后一个节点，而在开始时还没有节点。
curr 初始化为 head，表示当前正在处理的节点。
在 while 循环中，对于链表中的每一个节点，执行以下操作：
使用 next 临时保存 curr 的下一个节点。
将 curr 的 next 指针指向 prev，实现当前节点的反转。
将 prev 移动到 curr，curr 移动到 next。
循环继续直到 curr 为 nullptr，即到达原链表的末尾。
最后返回反转后的链表头:当循环结束时，prev 指向反转后链表的头节点，因此返回 prev。
```

### Ways
By C++:
```cpp
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
    ListNode* reverseList(ListNode* head) {
        ListNode *prev = nullptr;
        ListNode *curr = head;
        while (curr) {
            ListNode *next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
};
```

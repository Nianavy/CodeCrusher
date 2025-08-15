https://leetcode.cn/problems/insertion-sort-list

### Think
```txt
使用一个虚拟头节点和指针操作，它在原地对链表进行排序。每次找到需要插入的节点后，通过调整指针将其插入到正确的位置。
```

### Ways
By C++:
```cpp
class Solution {
public:
    ListNode* insertionSortList(ListNode* head) {
        if (head == nullptr) return head;
        ListNode *dummyHead = new ListNode(0);
        dummyHead->next = head;
        ListNode *lastSorted = head;
        ListNode *curr = head->next;
        while (curr != nullptr) {
            if (lastSorted->val <= curr->val) lastSorted = lastSorted->next;
            else {
                ListNode *prev = dummyHead;
                while (prev->next->val <= curr->val) prev = prev->next;
                lastSorted->next = curr->next;
                curr->next = prev->next;
                prev->next = curr;
            }
            curr = lastSorted->next;
        }
        return dummyHead->next;
    }
};
```

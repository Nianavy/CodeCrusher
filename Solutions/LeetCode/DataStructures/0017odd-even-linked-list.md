https://leetcode.cn/problems/odd-even-linked-list

### Think
```txt
重新排列一个单链表，使得所有奇数位置的节点排在前面，所有偶数位置的节点排在后面。这里的位置是从1开始计数的，所以第一个节点是奇数位置，第二个节点是偶数位置，依此类推。
```

### Ways
By C++:
```cpp
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        if (head == nullptr) return head;
        ListNode *evenHead = head->next;
        ListNode *odd = head;
        ListNode *even = evenHead;
        while (even != nullptr && even->next != nullptr) {
            odd->next = even->next;
            odd = odd->next;
            even->next = odd->next;
            even = even->next;
        }
        odd->next = evenHead;
        return head;
    }
};
```

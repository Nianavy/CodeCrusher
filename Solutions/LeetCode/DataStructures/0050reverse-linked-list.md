https://leetcode.cn/problems/reverse-linked-list

### Think
```TXT
当curr指针不为nullptr时保持循环，循环内容为将curr的next指针赋给一个新的next指针，
然后curr的next指针指向prev指针，prev在指向curr，curr再指向之前新next指针，循环结束后返回prev指针即可。
```

### Ways
By C++:
```C++
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

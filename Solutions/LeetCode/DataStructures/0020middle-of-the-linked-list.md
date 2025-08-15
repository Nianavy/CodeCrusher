https://leetcode.cn/problems/middle-of-the-linked-list

### Think
```txt
使用了“快慢指针”的技巧来找到链表的中间节点。慢指针每次走一步，快指针每次走两步。当快指针到达链表末尾时，慢指针正好在链表的中间。这个方法的时间复杂度是O(n)，其中n是链表的长度，因为每个节点最多被访问两次（一次被慢指针，一次被快指针）。
```

### Ways
By C++:
```cpp
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        ListNode *slow = head;
        ListNode *fast = head;
        while (fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
};
```

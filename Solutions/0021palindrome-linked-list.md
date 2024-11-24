https://leetcode.cn/problems/palindrome-linked-list

### Think
```txt
使用快慢指针来找到链表的中点，然后反转第二个链表的一半。最后，比较两个链表的一半来判断是否是回文链表。时间复杂度为 O(n) 和空间复杂度为 O(1)。
```

### Ways
By C++:
```cpp
class Solution {
public:
    bool isPalindrome(ListNode *head) {
        if (head == nullptr) return true;

        ListNode *firstHalfEnd = endOfFirstHalf(head);
        ListNode *secondHalfStart = reverseList(firstHalfEnd->next);

        ListNode *p1 = head;
        ListNode *p2 = secondHalfStart;
        bool result = true;
        while (result && p2 != nullptr) {
            if (p1->val != p2->val) result = false;
            p1 = p1->next;
            p2 = p2->next;
        }
        firstHalfEnd->next = reverseList(secondHalfStart);
        return result;
    }

    ListNode *reverseList(ListNode *head) {
        ListNode *prev = nullptr;
        ListNode *curr = head;
        while (curr != nullptr) {
            ListNode *nextTemp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }

    ListNode *endOfFirstHalf(ListNode *head) {
        ListNode *fast = head;
        ListNode *slow = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

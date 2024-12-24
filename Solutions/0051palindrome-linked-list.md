https://leetcode.cn/problems/palindrome-linked-list

### Think
```TXT
快慢指针
找到前半部分链表的尾节点。
反转后半部分链表。
判断是否回文。
恢复链表。
返回结果。
```

### Ways
By C++:
```C++
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

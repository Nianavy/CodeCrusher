# Question

[sort-list](https://leetcode.cn/problems/sort-list/)



# Answer

> solution

用归并排序处理链表，因其天然适合链表结构，并提供 O(n log n) 时间复杂度。

三步法实现
分割：用快慢指针找到链表中点，分割成两半（通过断开中点的 next 指针）。
递归：分别递归排序左右两半（自然分解到空或单链表）。
合并：用 dummy 头节点合并两个已排序的子链表。

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
static struct ListNode* getMiddle(struct ListNode* head) {
    struct ListNode *slow = head, *fast = head;
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}
static struct ListNode* merge(struct ListNode* left, struct ListNode* right) {
    struct ListNode dummy;
    struct ListNode *tail = &dummy;
    
    while (left && right) {
        if (left->val <= right->val) {
            tail->next = left;
            left = left->next;
        } else {
            tail->next = right;
            right = right->next;
        }
        tail = tail->next;
    }
    
    tail->next = left ? left : right;
    return dummy.next;
}
struct ListNode* sortList(struct ListNode* head) {
    if (!head || !head->next) return head;
    
    struct ListNode* mid = getMiddle(head);
    struct ListNode* right = mid->next;
    mid->next = NULL;
    
    return merge(sortList(head), sortList(right));
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
    ListNode* sortList(ListNode* head) {
        if (!head || !head->next) return head;
        
        // 找到中点
        ListNode* mid = getMiddle(head);
        ListNode* left = head;
        ListNode* right = mid->next;
        mid->next = nullptr;
        
        return merge(sortList(left), sortList(right));
    }
    
private:
    ListNode* getMiddle(ListNode* head) {
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast->next && fast->next->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
    
    ListNode* merge(ListNode* left, ListNode* right) {
        ListNode dummy(0);
        ListNode* tail = &dummy;
        
        while (left && right) {
            if (left->val <= right->val) {
                tail->next = left;
                left = left->next;
            } else {
                tail->next = right;
                right = right->next;
            }
            tail = tail->next;
        }
        
        tail->next = left ? left : right;
        return dummy.next;
    }
};
```

https://www.acwing.com/problem/content/34/

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* merge(ListNode* l1, ListNode* l2) {
      ListNode *temp = new ListNode(-1);
      ListNode *curr = temp;
      ListNode *p1 = l1;
      ListNode *p2 = l2;
      
      while (p1 != nullptr && p2 != nullptr) {
        if (p1->val <=  p2->val) {
          curr->next = p1;
          p1 = p1->next;
        }
        else {
          curr->next = p2;
          p2 = p2->next;
        }
        curr = curr->next;
      }
      if (p1 != nullptr) curr->next = p1;
      else curr->next =p2;
      
      ListNode *l3 = temp->next;
      delete temp;
      return l3;
    }
};
```

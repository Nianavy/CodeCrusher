https://www.acwing.com/problem/content/27/

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
    ListNode* deleteDuplication(ListNode* head) {
      if (head == nullptr || head->next == nullptr) return head;
      ListNode *temp = new ListNode(0);
      temp->next = head;
      ListNode *prev = temp;
      ListNode *curr = head;
      
      while (curr && curr->next) {
        if (curr->val == curr->next->val) {
          int val = curr->val;
          while (curr && curr->val == val) {
              ListNode *del = curr;
              curr = curr->next;
              delete del;
          }
          prev->next = curr;
        }
        else prev = curr, curr = curr->next;
      }
      ListNode * newHead = temp->next;
      delete temp;
      return newHead;
    }
};
```

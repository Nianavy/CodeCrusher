https://www.acwing.com/problem/content/18/

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
private:
    void printListReversinglyHelper(ListNode *node, vector<int> &result) {
      if (node->next != nullptr) printListReversinglyHelper(node->next, result);
      result.push_back(node->val);
    }
    
public:
    vector<int> printListReversingly(ListNode* head) {
      vector<int> result;
      if (head != nullptr) printListReversinglyHelper(head, result);
      return result;
    }
};
```

https://leetcode.cn/problems/delete-node-in-a-linked-list

### Think
```txt
取巧了。
题目保证了要删除的节点不是尾节点，这意味着要删除的节点node一定有下一个节点node->next。

因此，我们可以通过以下步骤来实现删除操作：

将当前节点node的值替换为下一个节点node->next的值，从而“覆盖”掉当前节点的值。
然后，通过修改当前节点的next指针，跳过下一个节点，直接指向下下一个节点，从而在逻辑上删除下一个节点。
这样，虽然我们没有直接删除当前节点node，但由于它的值已经被替换，并且它的next指针指向了更远的节点，所以从逻辑上，它相当于被删除了。
```

### Ways
By C++:
```cpp
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
    void deleteNode(ListNode* node) {
        node->val = node->next->val;
        node->next = node->next->next;
    }
};
```

https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii

### Think
```txt
虚拟头结点: 使用虚拟头结点 dummy 简化边界处理。
双指针: 使用 pre 和 cur 两个指针，pre 指向当前处理段的最后一个不重复节点，cur 用于遍历链表。
标记重复: duplicated 标记当前值是否重复。
内层循环: while (cur->next && cur->val == cur->next->val) 用于检测并跳过所有重复的节点。
删除重复节点: 如果 duplicated 为真，则 pre->next = cur->next; 跳过所有重复的节点。
移动指针: 如果 duplicated 为假，则 pre = cur; 移动 pre 指针。最后，cur = cur->next; 移动 cur 指针。
返回结果: 返回 dummy->next。
```

### Ways
By C++:
```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* dummy = new ListNode(0, head);
        ListNode* pre = dummy;
        ListNode* cur = head;

        while (cur) {
            bool duplicated = false;
            while (cur->next && cur->val == cur->next->val) {
                duplicated = true;
                cur = cur->next;
            }

            if (duplicated) {
                pre->next = cur->next; // 跳过所有重复的节点
            } else {
                pre = cur; // 移动 pre 指针
            }
            cur = cur->next; // 移动 cur 指针
        }

        return dummy->next;
    }
};
```

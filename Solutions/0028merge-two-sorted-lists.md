https://leetcode.cn/problems/merge-two-sorted-lists

### Think
```txt
哨兵节点：

创建一个哨兵节点 preHead（值可以是任意，比如 -1），它的 next 将指向最终合并链表的头节点。
使用一个指针 prev 来跟踪合并链表的尾部，初始指向 preHead。
比较并连接节点：

使用一个 while 循环，比较两个链表的当前节点 l1 和 l2 的值：
如果 l1 的值较小，将 l1 连接到 prev 的 next，然后移动 l1 指针。
否则，将 l2 连接到 prev 的 next，然后移动 l2 指针。
同时，移动 prev 指针到刚刚连接的节点。
处理剩余部分：

当一个链表遍历完后，直接将另一个链表的剩余部分连接到 prev 的 next。
返回结果：

返回 preHead->next，因为 preHead 是哨兵节点，它的 next 才是合并后链表的真正头节点。
```

### Ways
By C++:
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *preHead = new ListNode(-1);

        ListNode *prev = preHead;
        while (l1 != nullptr && l2 != nullptr) {
            if (l1->val < l2->val) {
                prev->next = l1;
                l1 = l1->next;
            } else {
                prev->next = l2;
                l2 = l2->next;
            }
            prev = prev->next;
        }
        prev->next = l1 == nullptr ? l2 : l1;
        return preHead->next;
    }
};
```

https://leetcode.cn/problems/partition-list

### Think
```txt
创建虚拟头节点：

small 和 smallHead：用于存储所有小于 x 的节点。
large 和 largeHead：用于存储所有大于或等于 x 的节点。
这些虚拟头节点的值为 0，但实际值并不重要，因为它们只是用来简化链表操作的。
遍历原链表：

使用 while 循环遍历原链表 head。
对于每个节点，检查其值是否小于 x：
如果小于 x，将其添加到 small 链表中。
否则，将其添加到 large 链表中。
更新 small 和 large 指针，使其指向新添加的节点。
移动 head 指针到下一个节点。
处理链表末尾：

将 large 链表的末尾指向 nullptr，防止形成环。
将 small 链表的末尾连接到 large 链表的头部。
返回新的链表头节点：

返回 smallHead->next，即 small 链表的第一个实际节点。
```

### Ways
By C++:
```cpp
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
    ListNode* partition(ListNode* head, int x) {
        ListNode *small = new ListNode(0);
        ListNode *smallHead = small;
        ListNode *large = new ListNode(0);
        ListNode *largeHead = large;
        while (head != nullptr) {
            if (head->val < x) {
                small->next = head;
                small = small->next;
            }
            else {
                large->next = head;
                large = large->next;
            }
            head = head->next;
        }
        large->next = nullptr;
        small->next = largeHead->next;
        return smallHead->next;
    }
};
```

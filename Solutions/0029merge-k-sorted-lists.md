https://leetcode.cn/problems/merge-k-sorted-lists

### Think
```txt
使用**优先队列（最小堆）**来高效地合并 k 个有序链表。可以把它想象成一场“选秀比赛”，每个链表的当前最小值都是参赛选手，优先队列就是评委席。

初始化优先队列：  把每个链表的头节点放入优先队列中。由于优先队列的特性，堆顶元素始终是当前所有链表头节点中最小的那个。

迭代合并：

取出堆顶元素（当前最小节点），把它链接到结果链表的尾部。
如果取出的节点还有后续节点，就把它的后续节点放入优先队列，相当于下一轮比赛的选手入场。
循环直到优先队列为空：  当优先队列为空时，表示所有链表的节点都已经被合并到结果链表中了。
```

### Ways
By C++:
```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        auto cmp = [](const ListNode* a, const ListNode* b) {
            return a->val > b->val;
        };
        priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> pq(cmp);

        for (const auto& head : lists) {
            if (head) {
                pq.push(head);
            }
        }

        if (pq.empty()) {
            return nullptr;
        }

        ListNode* dummy = new ListNode();
        ListNode* cur = dummy;

        while (!pq.empty()) {
            ListNode* node = pq.top();
            pq.pop();
            cur->next = node;
            cur = cur->next;
            if (node->next) {
                pq.push(node->next);
            }
        }

        ListNode* result = dummy->next;
        delete dummy; 
        return result;
    }
};
```

https://leetcode.cn/problems/split-linked-list-in-parts

### Think
```txt
计算链表长度: 首先遍历链表，统计节点总数 n。

计算每份基础长度和余数:  计算 quotient = n / k  得到每份的基础长度，以及 remainder = n % k 得到无法均分的剩余节点数。

分配节点: 遍历 k 次，创建 k 个子链表。

前 remainder 个子链表，每个链表的长度为 quotient + 1，也就是基础长度加上一个剩余节点。
剩余的 k - remainder 个子链表，每个链表的长度为 quotient。
断开连接: 在分配节点的过程中，使用 curr->next = nullptr; 断开子链表与原链表的连接，避免后续遍历出错。

循环内部的 for 循环使用一个临时变量 tail 指向当前子链表的尾部，减少一次循环。
```

### Ways
By C++:
```cpp
class Solution {
public:
    vector<ListNode*> splitListToParts(ListNode* head, int k) {
        int n = 0;
        ListNode *temp = head;
        while (temp != nullptr) {
            ++n;
            temp = temp->next;
        }
        int quotient = n / k, remainder = n % k;

        vector<ListNode*> parts(k, nullptr);
        ListNode *curr = head;
        for (int i = 0; i < k && curr != nullptr; ++i) {
            parts[i] = curr;
            int partSize = quotient + (i < remainder ? 1 : 0);

            ListNode *tail = curr;
            for (int j=1; j<partSize; ++j) tail = tail->next;
            curr = tail->next;
            tail->next = nullptr;
        }
        return parts;
    }
};
```

https://leetcode.cn/problems/reorder-list

### Think
```txt
利用线性表的的下标访问重建此链表
```
### Ways
By C++:
```cpp
class Solution {
public:
    void reorderList(ListNode* head) {
        if (head == nullptr) return;
        vector<ListNode *> vec;
        ListNode *node = head;
        while (node != nullptr) {
            vec.emplace_back(node);
            node = node->next;
        }
        int i=0, j = vec.size() - 1;
        while (i<j) {
            vec[i]->next = vec[j];
            ++i;
            if (i == j) break;
            vec[j]->next = vec[i];
            --j;
        }
        vec[i]->next = nullptr;
    }
};
```

https://leetcode.cn/problems/swap-nodes-in-pairs

### Think
```txt
循环迭代法
```
### Ways
By C++:
```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        // 创建一个虚拟头节点，它的值为0，指向链表的头节点
        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;
        
        // 定义一个临时节点，指向虚拟头节点
        ListNode* temp = dummyHead;
        
        // 当temp的下一个节点和下下个节点都存在时，进入循环
        while (temp->next != nullptr && temp->next->next != nullptr) {
            // 第一个需要交换的节点
            ListNode* node1 = temp->next;
            // 第二个需要交换的节点
            ListNode* node2 = temp->next->next;
            
            // 将temp的下一个节点直接指向node2，跳过node1
            temp->next = node2;
            
            // 将node1的next指向node2的next，完成node1的后续连接
            node1->next = node2->next;
            
            // 将node2的next指向node1，完成node2的前向连接
            node2->next = node1;
            
            // 移动temp到node1，准备下一轮交换
            temp = node1;
        }
        
        // 获取新的头节点（虚拟头节点的下一个节点）
        ListNode* ans = dummyHead->next;
        
        // 删除虚拟头节点，释放内存
        delete dummyHead;
        
        // 返回新的头节点
        return ans;
    }
};
```

https://leetcode.cn/problems/remove-linked-list-elements

### Think
```txt
迭代即可
```
1. **定义链表节点结构**：`ListNode`结构体包含一个整数值`val`和一个指向下一个节点的指针`next`。提供了三个构造函数，分别用于默认构造、带值构造和带值及下一个节点构造。
   
2. **`removeElements`函数**：该函数接受两个参数，头节点`head`和要删除的值`val`。首先，创建一个虚拟头节点`dummyHead`，其`next`指针指向链表的头节点。这是为了方便处理可能需要删除头节点的情况。

3. **遍历链表**：创建一个临时指针`temp`，初始指向虚拟头节点。使用`while`循环遍历链表，直到到达链表末尾。在循环中，检查`temp->next`节点的值是否等于`val`。

4. **删除节点**：如果`temp->next`节点的值等于`val`，则通过`temp->next = temp->next->next`语句跳过该节点，从而实现删除操作。否则，将`temp`指针移动到下一个节点。

5. **返回结果**：最后返回`dummyHead->next`，即新的头节点。这种方法确保了即使头节点需要被删除，也能正确处理，因为头节点本身也可能被跳过。

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
    ListNode* removeElements(ListNode* head, int val) {
        struct ListNode *dummyHead = new ListNode(0, head);
        struct ListNode *temp = dummyHead;
        while (temp->next != nullptr) {
            if (temp->next->val == val) temp->next = temp->next->next;
            else temp = temp->next;
        }
        return dummyHead->next;
    }
};
```

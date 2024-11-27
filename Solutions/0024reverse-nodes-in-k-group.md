https://leetcode.cn/problems/reverse-nodes-in-k-group

### Think
```txt
模拟头结点 hair:  创建一个虚拟头结点hair，简化边界处理，使代码更简洁。

双指针 pre 和 tail: pre 指向前一组翻转后的尾结点，tail 指向当前组的尾结点。

子链表翻转 myReverse(head, tail):  这个函数负责翻转head到tail之间的子链表，并返回新的头尾结点。它巧妙地使用了迭代方式进行翻转，避免了递归带来的栈空间开销。

循环处理:  外层循环以k个节点为一组进行处理。每次循环：

先用tail指针向后移动k步，检查剩余链表长度是否足够k。
如果不足，直接返回结果。
如果足够，调用myReverse翻转子链表。
将翻转后的子链表重新连接到原链表中。
更新pre和head指针，准备下一轮翻转。
```

### Ways
By C++:
```cpp
class Solution {
public:
    // 翻转一个子链表，并且返回新的头与尾
    pair<ListNode*, ListNode*> myReverse(ListNode* head, ListNode* tail) {
        ListNode* prev = tail->next;
        ListNode* p = head;
        while (prev != tail) {
            ListNode* nex = p->next;
            p->next = prev;
            prev = p;
            p = nex;
        }
        return {tail, head};
    }

    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* hair = new ListNode(0);
        hair->next = head;
        ListNode* pre = hair;

        while (head) {
            ListNode* tail = pre;
            // 查看剩余部分长度是否大于等于 k
            for (int i = 0; i < k; ++i) {
                tail = tail->next;
                if (!tail) {
                    return hair->next;
                }
            }
            ListNode* nex = tail->next;
            tie(head, tail) = myReverse(head, tail);
            // 把子链表重新接回原链表
            pre->next = head;
            tail->next = nex;
            pre = tail;
            head = tail->next;
        }

        return hair->next;
    }
};
```

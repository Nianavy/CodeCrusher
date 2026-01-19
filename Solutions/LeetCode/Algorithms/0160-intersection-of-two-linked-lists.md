# Question

[intersection-of-two-linked-lists](https://leetcode.cn/problems/intersection-of-two-linked-lists/)



# Answer

> solution

**核心思路：双指针，两次遍历**

1.  **无脑初始化**：从两个链表的头节点开始，两个指针 `p` 和 `q` 同时‘前进’。
2.  **交换遍历**：当 `p` 到达链表 A 的末尾时，立即切换到链表 B 的头部继续遍历。`q` 同理。
3.  **相遇即交点**：遍历过程中，如果两个指针不相等，就继续更新。**若有交点**，由于总遍历路径长度相等（`lenA + lenB`），它们必然在交点处相遇。**若无交点**，它们会在最后同时变为 `NULL`，此时循环结束，返回 `NULL`。
4.  **零修改**：整个过程只读不写，绝不改变原链表结构。

**为什么好？**
- **纯粹**：没有额外循环，没有临时变量，逻辑内聚。
- **直接**：将“寻找交点”转化为“使两段路径在必要时交换起点，最终在同一点相遇”，一气呵成。

一言蔽之：**用协调一致的步伐遍历两张表，让总路程你们走的一样长，交点自然就会显现。**

### Way Of C

> source code

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode *getIntersectionNode(struct ListNode *headA, struct ListNode *headB) {
    // 基础的NULL检查是必要的，C标准没有bool类型。
    if (headA == NULL || headB == NULL) {
        return NULL;
    }
    struct ListNode *p = headA;
    struct ListNode *q = headB;
    while (p != q) {
        // C语言中，`p = p ? p->next : headB;` 这样写是野路子。
        // 更清晰、更标准的写法是使用if语句。
        if (p == NULL) {
            p = headB;
        } else {
            p = p->next;
        }
        if (q == NULL) {
            q = headA;
        } else {
            q = q->next;
        }
    }
    return p; // 可能是交点，也可能是NULL。
}
```

---

### Way Of C++

> source code

```c++
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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (!headA || !headB) {
            return nullptr;
        }
        ListNode *p = headA;
        ListNode *q = headB;
        // 当遍历完A后切换到B，遍历完B后切换到A。
        // 如果有交点，p和q会在交点相遇。
        // 如果没有交点，p和q会同时变成nullptr。
        while (p != q) {
            p = p ? p->next : headB;
            q = q ? q->next : headA;
        }
        return p; // 可能是交点，也可能是nullptr。
    }
};
```

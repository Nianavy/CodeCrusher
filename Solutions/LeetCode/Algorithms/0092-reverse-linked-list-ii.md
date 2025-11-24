# Question

[reverse-linked-list-ii](https://leetcode.cn/problems/reverse-linked-list-ii/)



# Answer

> solution

### 头插法（Head Insertion）流程

解决该问题的思路是找到子链表起点，然后用 `right - left` 次操作，将后续节点依次移动到子链表的头部。

1.  **准备阶段（哑节点和定位）**
    *   创建栈上的哑节点 `dummy`，让它指向 `head`。
    *   使用 `start_prev` 从 `dummy` 开始，向前移动 `left - 1` 步，定位到反转区间的**前一个节点**。
    *   确定 `sublist_tail` 为 `start_prev->next` (即原始的第 `left` 个节点)。

2.  **反转循环 (n-m 次)**
    循环 `right - left` 次，每次执行以下三步，目标是移动 `sublist_tail` 后面的节点 `to_move`：

    | 步骤 | 行为 | 目标 | 作用 |
    | :--- | :--- | :--- | :--- |
    | **1. 摘除** | `sublist_tail->next = to_move->next;` | 将 `to_move` 从链表中删除。 | 确保 `sublist_tail` 始终指向反转后尾部的正确下一个节点（即原始的 `right+1` 节点）。 |
    | **2. 重定向** | `to_move->next = start_prev->next;` | `to_move` 指向当前反转子链表的头部。 | 建立新的反转链接。 |
    | **3. 插入** | `start_prev->next = to_move;` | `to_move` 成为新的子链表头部。 | 将 `to_move` 插入到 `start_prev` 之后。 |

3.  **收尾**
    返回 `dummy.next`。由于 `dummy` 是栈分配，无需担心内存泄漏。

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
struct ListNode* reverseBetween(struct ListNode* head, int left, int right) {
    if (left == right)
        return head;

    // 1. 创建栈上的哑节点 (Dummy Node)
    // 这种方式最安全，避免了堆分配和手动清理
    struct ListNode dummy = {0, head}; 
    struct ListNode *start_prev = &dummy;

    // 2. 找到反转区间起点的前一个节点 (start_prev)
    for (int i = 1; i < left; i++) {
        start_prev = start_prev->next;
        // 如果 left 超出链表长度，这里会遇到 NULL，但根据题意，通常假设 left/right 是有效的。
        if (!start_prev) return head; 
    }

    // sublist_tail：反转后将成为子链表尾部的节点（原始的第 left 个节点）
    struct ListNode *sublist_tail = start_prev->next; 

    // 3. 使用头插法，执行 (right - left) 次操作
    for (int i = 0; i < right - left; i++) {
        
        // to_move：需要移动到头部的节点
        struct ListNode *to_move = sublist_tail->next;

        // --- 头插法三步曲 ---
        
        // a) 摘除：sublist_tail 跳过 to_move 节点
        sublist_tail->next = to_move->next;
        
        // b) 连接：to_move 节点指向当前反转子链表的头部
        to_move->next = start_prev->next;
        
        // c) 插入：start_prev 指向 to_move，to_move 成为新的子链表头部
        start_prev->next = to_move;
    }

    // 4. 返回：返回哑节点后面的真正头部
    return dummy.next;
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
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (left == right)
            return head;

        // 1. 创建栈上的哑节点 (Dummy Node)，避免头节点被操作的特殊情况
        // 这是处理链表操作最简洁且内存安全的方式。
        // 使用一个未命名的临时对象作为哑节点。
        ListNode dummy(0, head); 
        
        ListNode* start_prev = &dummy;

        // 2. 找到反转区间起点的前一个节点 (start_prev)
        // 循环执行 left - 1 次
        for (int i = 1; i < left; ++i) {
            start_prev = start_prev->next;
        }

        // sublist_tail：反转后将成为子链表尾部的节点（即原始的第 left 个节点）
        ListNode* sublist_tail = start_prev->next; 

        // 3. 使用头插法，执行 (right - left) 次移动操作
        // 每次将 sublist_tail 后面的节点摘下，插入到 start_prev 之后。
        for (int i = 0; i < right - left; ++i) {
            
            // to_move：需要移动到头部的节点
            ListNode* to_move = sublist_tail->next;

            // --- 头插法三步曲 ---
            
            // a) 摘除：sublist_tail 跳过 to_move 节点
            // 此时 sublist_tail->next 指向 to_move 的下一个节点
            sublist_tail->next = to_move->next;
            
            // b) 连接：to_move 节点指向当前反转子链表的头部
            to_move->next = start_prev->next;
            
            // c) 插入：start_prev 指向 to_move，to_move 成为新的子链表头部
            start_prev->next = to_move;
        }

        // 4. 返回：由于 dummy 节点在栈上，函数退出后自动清理。
        return dummy.next;
    }
};
```

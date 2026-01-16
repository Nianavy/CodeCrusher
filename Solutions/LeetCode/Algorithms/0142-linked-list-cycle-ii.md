# Question

[linked-list-cycle-ii](https://leetcode.cn/problems/linked-list-cycle-ii/)



# Answer

> solution

### **1. 核心武器：快慢指针**

在只能单向遍历的链表里追踪环，唯一能用的工具是两个指针，一个走快，一个走慢。这就是“龟兔赛跑”算法的全部原理。

### **2. 两步走**

**第一步：找交点**
让快指针（每次2步）和慢指针（每次1步）从**同一个起点**（头部）出发。如果有环，快指针最终会从后面追上慢指针，它们在环里相遇。

**第二步：找入口**
第一次相遇后，把慢指针放回头部。然后两个指针**都只每次1步**向前走。当它们再次相遇时，那个节点就是环的入口。

### **3. 为什么这样能工作？**

设链表尾部到环入口距离为 L，环周长为 C。
*   第一次相遇时，可以推导出：从相遇点到环入口的距离 = 从头部到环入口的距离。
*   所以，两个指针从“头部”和“相遇点”同时以相同速度（1步/次）出发，必然会同时到达环入口。

### **4. 关键优势**

*   **简单高效**：只用了两个指针，时间O(n)，空间O(1)。
*   **自动处理**：空链表、无环链表等情况，循环条件(`fast && fast->next`) 会自动搞定，无需特殊处理。
*   **你的代码问题**：你第一次尝试让快指针从 `head->next` 开始是错的，这破坏了算法的数学基础。你提供的第二次C++和C版本是标准解法，完全正确。

### **5. 结论**

用快慢指针分两步走，这是解决此问题的唯一正确、最直接、最简洁的方法。

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
struct ListNode *detectCycle(struct ListNode *head) {
    if (head == NULL || head->next == NULL) {
        return NULL;
    }
    struct ListNode *slow = head;
    struct ListNode *fast = head;
    // 第一阶段：找到相遇点
    while (fast != NULL && fast->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) {
            // 第二阶段：找到环入口
            slow = head;
            while (slow != fast) {
                slow = slow->next;
                fast = fast->next;
            }
            return slow;
        }
    }
    return NULL;
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
    ListNode* detectCycle(ListNode *head) {
        // 处理空链表或只有一个节点的情况
        if (!head || !head->next) {
            return nullptr;
        }
        ListNode *slow = head;
        ListNode *fast = head;
        // 第一阶段：找到快慢指针的相遇点
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) {
                // 第二阶段：重置慢指针，同步移动找到环入口
                slow = head;
                while (slow != fast) {
                    slow = slow->next;
                    fast = fast->next;
                }
                return slow;
            }
        }
        return nullptr;
    }
};
```

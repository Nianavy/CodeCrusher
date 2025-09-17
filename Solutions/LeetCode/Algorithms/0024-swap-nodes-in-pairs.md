# Question

[swap-nodes-in-pairs](https://leetcode.cn/problems/swap-nodes-in-pairs/)



# Answer

> solution

解决“两两交换链表中的节点”问题的核心思路是**迭代法**，并巧妙地利用一个**虚拟头节点**来简化边缘情况处理。本质上就是通过调整指针，将每两个相邻的节点进行“物理位置”上的对调。

### **核心算法思路 (C++ 和 C 共享)**

1.  **虚拟头节点 (Dummy Node)**：
    *   **作用**：为了简化对链表头部的操作。如果原始头节点 `head` 需要参与交换，那么它的“前一个节点”就不存在了。虚拟头节点充当 `head` 的前一个节点，统一了所有节点的处理逻辑，避免了特殊判断。
    *   **实现**：创建一个临时的 `ListNode dummy;` (C++) 或 `struct ListNode dummy = { .val = 0, .next = NULL };` (C)。将 `dummy.next` 指向原始链表的 `head`。
    *   **Linus 哲学**：这是“好品味”的体现。它通过引入一个辅助节点，消除边界条件，让核心逻辑更简洁、更通用。

2.  **“前一个节点”指针 (Previous Pointer)**：
    *   **作用**：用一个指针 `p` (或者叫 `prev`) 来始终指向当前处理的**一对节点的前一个节点**。这个 `p` 的 `next` 指针，将在交换后指向新的第一个节点。
    *   **初始化**：`p` 从虚拟头节点 `&dummy` 开始。

3.  **迭代处理**：
    *   **循环条件**：只要 `p` 的后面至少还有两个节点 (`p->next != nullptr && p->next->next != nullptr`)，就继续循环。这是因为我们要交换一对节点，需要确保有足够多的节点。
    *   **获取待交换节点**：
        *   `a = p->next`：当前对中的第一个节点。
        *   `b = a->next`：当前对中的第二个节点。
    *   **执行交换操作（调整指针）**：这是最关键的步骤，需要精确地修改 3 个 `next` 指针：
        1.  **`p->next = b;`**：将 `p` 的 `next` 指向 `b`。这意味着 `b` 现在成为了这一对中新的第一个节点，并连接到了 `p` 后面。
        2.  **`a->next = b->next;`**：将 `a` 的 `next` 指向 `b` 原来的 `next`。这意味着 `a` 现在连接到了 `b` 后面剩下的链表。
        3.  **`b->next = a;`**：将 `b` 的 `next` 指向 `a`。至此，`p -> b -> a -> (原来的 b->next)` 的链接关系已经建立完成。
    *   **更新“前一个节点”指针**：
        *   `p = a;`：经过交换后，原来的 `a` 现在是新对中的第二个节点（在 `b` 之后）。它自然地成为了下一轮循环中要处理的下一对节点的前一个节点。

4.  **返回结果**：
    *   循环结束后，虚拟头节点的 `next` 指向的就是两两交换后的新链表头部。返回 `dummy.next`。

### **C++ 版本和 C 版本在实现细节上的核心差异**

核心算法思想是完全相同的，因为问题本身就是对底层指针的操作。两者的差异主要体现在语言特性和内存管理习惯上：

#### **C++ 版本解决问题的核心**

*   **栈分配的 `ListNode`**：`ListNode dummy;` 在栈上创建虚拟头节点，避免了 `new ListNode(-1)` 带来的内存泄漏问题。这是 C++ 中在无法返回智能指针时，处理辅助节点的**推荐实践**。
*   **类封装**：`class Solution { public: ... };` 结构是 C++ 解决 LeetCode 这类问题的标准方式，尽管在这个简单问题中，它只是一个命名空间。
*   **`nullptr` 关键字**：相比 C 的 `NULL`，`nullptr` 提供了更好的类型安全性。

#### **C 版本解决问题的核心**

*   **栈分配的 `struct ListNode`**：`struct ListNode dummy = { .val = 0, .next = NULL };` 与 C++ 相同，利用栈来避免额外的堆内存分配和释放。这是 C 语言中的**高效实践**。
*   **原始指针操作**：C 语言直接使用原始指针进行链表操作，与 C++ 在这方面基本一致，都要求对指针的生命周期和指向有清晰的认识。
*   **内存管理责任**：对于输入链表 `head`，此函数不涉及节点的新建或删除，只重排了现有节点的连接顺序。因此，原始链表的内存管理（创建和释放）责任完全在调用者。

**总而言之，无论 C++ 还是 C，解决此问题的关键都在于：使用一个栈上的虚拟头节点来简化边界条件处理，然后通过迭代和精确调整三个关键指针 (`p->next`, `a->next`, `b->next`) 来完成每对节点的交换，最终返回虚拟头节点的下一个节点。**

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
struct ListNode* swapPairs(struct ListNode* head) {
    // 使用栈上的一个虚拟头节点。
    struct ListNode dummy = { .val = 0, .next = NULL };
    dummy.next = head; // 虚拟头节点指向原始链表的头部

    // `p` (prev) 是当前遍历到的“前一个节点”。
    // 它的 `next` 指针将指向被交换后的第一对中的第二个节点。
    // `p` 从 dummy 开始，因为 dummy 是 head 的前一个节点。
    struct ListNode* p = &dummy;

    // 循环条件：确保至少还有两个节点可以交换
    // (p->next 是第一个，p->next->next 是第二个)
    while (p->next != NULL && p->next->next != NULL) {
        // `a` 是当前对的第一个节点
        // `b` 是当前对的第二个节点
        struct ListNode* a = p->next;
        struct ListNode* b = a->next;

        // 执行交换操作：
        // 1. p 的 next 指向 b (将 b 插入到 p 之后)
        p->next = b;
        // 2. a 的 next 指向 b 的旧 next (将 a 连接到 b 之后)
        a->next = b->next;
        // 3. b 的 next 指向 a (完成 a 和 b 的交换)
        b->next = a;

        // 移动 p 到新的“前一个节点”的位置，为下一轮交换做准备。
        // 经过交换，原来的 `a` 现在是交换后的第二个节点，它成为下一对的前一个节点。
        p = a;
    }

    // 返回虚拟头节点的下一个节点，即交换后的新链表头部。
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
    ListNode* swapPairs(ListNode* head) {
        // 使用栈上的一个虚拟头节点。这是避免内存泄漏的关键。
        ListNode dummy;
        dummy.next = head; // 虚拟头节点指向原始链表的头部

        // `p` 是当前遍历到的“前一个节点” (previous node)。
        // 它的 `next` 指针将指向被交换后的第一对中的第二个节点。
        // `p` 从 dummy 开始，因为 dummy 是 head 的前一个节点。
        ListNode* p = &dummy;

        // 循环条件：确保至少还有两个节点可以交换 (p->next 是第一个，p->next->next 是第二个)
        // 简写: p->next && p->next->next
        while (p->next != nullptr && p->next->next != nullptr) {
            // `a` 是当前对的第一个节点 (例如 1 -> 2 -> 3 -> 4 中的 1)
            // `b` 是当前对的第二个节点 (例如 1 -> 2 -> 3 -> 4 中的 2)
            ListNode* a = p->next;
            ListNode* b = a->next;

            // 执行交换操作：
            // 1. p 的 next 指向 b (将 b 插入到 p 之后)
            p->next = b;
            // 2. a 的 next 指向 b 的旧 next (将 a 连接到 b 之后)
            a->next = b->next;
            // 3. b 的 next 指向 a (完成 a 和 b 的交换)
            b->next = a;

            // 移动 p 到新的“前一个节点”的位置，为下一轮交换做准备。
            // 经过交换，原来的 `a` 现在是交换后的第二个节点，它成为下一对的前一个节点。
            p = a;
        }

        // 返回虚拟头节点的下一个节点，即交换后的新链表头部。
        return dummy.next;
    }
};
```

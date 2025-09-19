# Question

[reverse-nodes-in-k-group](https://leetcode.cn/problems/reverse-nodes-in-k-group/)



# Answer

> solution

解决“K 个一组翻转链表”问题的**核心思路是递归 (Recursion)**，它将一个大问题分解为两个更小、更容易管理的部分：
1.  **翻转当前这一组 `k` 个节点。**
2.  **递归地处理链表的剩余部分。**
然后将这两部分正确地连接起来。这种分而治之的策略使得复杂的指针操作变得清晰可控。

### **核心算法思路 (C++ 和 C 共享)**

1.  **检查当前组是否有效 (`count` 逻辑)**：
    *   **目标**：首先要确定当前从 `head` 开始的链表片段是否有至少 `k` 个节点可以进行翻转。
    *   **实现**：使用一个 `current` 指针向前走 `k` 步，并用一个 `count` 计数。
    *   **剪枝/边界情况**：
        *   如果 `head` 为空，或者 `k == 1` (翻转自身无意义)，直接返回 `head`。
        *   如果在走 `k` 步的过程中发现链表节点不足 `k` 个 (`count < k`)，那么这部分链表不应该被翻转。直接返回原始的 `head`。

2.  **翻转当前 `k` 个节点**：
    *   **目标**：将 `head` 开始的 `k` 个节点进行局部翻转。
    *   **实现**：使用经典的**三指针翻转法** (`prev`, `curr`, `next_node`)。
        *   `prev`：指向翻转后 `curr` 节点的前一个节点。初始为 `nullptr`。
        *   `curr`：当前正在处理的节点。初始为 `head`。
        *   `next_node`：`curr->next` 的临时存储，在修改 `curr->next` 之前保存下一个节点。
    *   **循环**：进行 `k` 次迭代，每次迭代完成一个节点的翻转：
        1.  保存 `curr->next` 到 `next_node`。
        2.  将 `curr->next` 指向 `prev` (完成翻转)。
        3.  更新 `prev = curr` (将 `prev` 移动到已翻转部分的头部)。
        4.  更新 `curr = next_node` (将 `curr` 移动到未翻转部分的头部)。

3.  **连接递归结果**：
    *   **目标**：将翻转后的当前 `k` 个节点，与链表剩余部分翻转后的结果连接起来。
    *   **关键点**：
        *   **翻转前的 `head`** (即 `k` 个节点中的第一个节点) 在翻转后会变成这 `k` 个节点中的**最后一个节点**。它的 `next` 指针应该指向**下一组 `k` 个节点翻转后的新头部**。
        *   **翻转后的 `prev`** (即 `k` 个节点中的第 `k` 个节点) 在翻转后会变成这 `k` 个节点中的**第一个节点**。它就是当前组翻转后的**新头部**。
    *   **实现**：
        1.  `head->next = reverseKGroup(curr, k);`：原来的 `head` 现在是当前翻转组的尾部。它的 `next` 指向对 `curr` (即下一组的旧头部) 递归调用 `reverseKGroup` 后的结果。
        2.  `return prev;`：返回 `prev`，因为它现在是当前翻转组的新头部。

### **C++ 版本和 C 版本在实现细节上的核心差异**

核心算法是完全一致的，因为这是一种纯粹的算法逻辑，不依赖于高级语言特性。

#### **C++ 版本解决问题的核心**

*   **类封装**：`class Solution { public: ... };` 结构是 C++ 中典型的 LeetCode 风格，将函数作为成员方法。
*   **`nullptr`**：提供更好的类型安全性，是 C++11 及以后推荐使用的空指针字面量。
*   **构造函数**：`ListNode` 的构造函数提供了方便的节点初始化方式。

#### **C 版本解决问题的核心**

*   **结构体定义**：使用 `struct ListNode` 定义链表节点。
*   **`NULL`**：作为空指针宏。
*   **手动内存管理**：对于测试辅助函数（如 `createNode`），需要手动 `malloc` 和 `free` 节点。然而，`reverseKGroup` 函数本身**不涉及新的节点分配或删除**，它只修改现有节点的 `next` 指针，所以它内部没有内存管理责任。

**总结来说，解决“K 个一组翻转链表”问题的核心在于采用递归策略：首先判断当前片段是否有 `k` 个节点可以翻转；如果有，则用三指针法进行局部翻转；然后将当前翻转组的旧头节点（现在是尾部）连接到剩余链表递归翻转后的新头部，最后返回当前翻转组的新头部。这种分解和组合的思路，是处理复杂链表问题的“好品味”体现。**

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
struct ListNode* reverseKGroup(struct ListNode* head, int k) {
    // 1. 检查链表是否为空或 k 为 1
    if (head == NULL || k == 1) {
        return head;
    }

    // 2. 检查当前段是否有 k 个节点
    struct ListNode* current = head;
    int count = 0;
    while (current != NULL && count < k) {
        current = current->next;
        count++;
    }

    // 如果不足 k 个节点，则不翻转，直接返回当前头部
    if (count < k) {
        return head;
    }

    // 3. 翻转当前 k 个节点
    // 使用三指针法进行局部翻转
    struct ListNode* prev = NULL;
    struct ListNode* curr = head;
    struct ListNode* next_node = NULL; // 用于临时保存下一个节点

    // 注意这里只翻转 k 个节点
    for (int i = 0; i < k; ++i) {
        next_node = curr->next; // 保存下一个节点
        curr->next = prev;      // 当前节点的 next 指向前一个节点 (翻转)
        prev = curr;            // prev 移动到当前节点 (它将成为翻转后的新头部)
        curr = next_node;       // curr 移动到下一个节点 (它将是下一组的旧头部)
    }

    // 4. 递归处理剩余部分并连接
    // `head` (原来的第一个节点) 现在是翻转后的最后一个节点。
    // 它的 `next` 应该指向下一组翻转后的头部。
    // `curr` (翻转前的 `k+1` 个节点) 是下一组的头部。
    head->next = reverseKGroup(curr, k);

    // `prev` (原来的第 k 个节点) 现在是翻转后的第一个节点。
    // 它就是当前组翻转后的新头部，应该作为结果返回。
    return prev;
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
    ListNode* reverseKGroup(ListNode* head, int k) {
        // 1. 检查链表是否为空
        if (head == nullptr || k == 1) {
            return head;
        }

        // 2. 检查当前段是否有 k 个节点
        ListNode* current = head;
        int count = 0;
        while (current != nullptr && count < k) {
            current = current->next;
            count++;
        }

        // 如果不足 k 个节点，则不翻转，直接返回当前头部
        if (count < k) {
            return head;
        }

        // 3. 翻转当前 k 个节点
        // 使用三指针法进行局部翻转
        ListNode* prev = nullptr;
        ListNode* curr = head;
        ListNode* next_node = nullptr; // 用于临时保存下一个节点

        // 注意这里只翻转 k 个节点
        for (int i = 0; i < k; ++i) {
            next_node = curr->next; // 保存下一个节点
            curr->next = prev;      // 当前节点的 next 指向前一个节点 (翻转)
            prev = curr;            // prev 移动到当前节点
            curr = next_node;       // curr 移动到下一个节点
        }

        // 4. 递归处理剩余部分并连接
        // `head` (原来的第一个节点) 现在是翻转后的最后一个节点。
        // 它的 `next` 应该指向下一组翻转后的头部。
        // `curr` (翻转前的 `k+1` 个节点) 是下一组的头部。
        head->next = reverseKGroup(curr, k);

        // `prev` (原来的第 k 个节点) 现在是翻转后的第一个节点。
        // 它就是当前组翻转后的新头部，应该作为结果返回。
        return prev;
    }
};
```

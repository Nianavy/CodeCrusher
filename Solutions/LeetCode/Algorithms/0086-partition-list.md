# Question

[partition-list](https://leetcode.cn/problems/partition-list/)



# Answer

> solution

链表分区 (Partition List)

解决链表分区问题的核心在于在保持节点**相对顺序不变**的前提下，将节点分为两组。

#### 核心哲学：用简单的数据结构取代复杂的指针重排。

1.  **方法选择：双链表法（Split & Merge）**
    *   **放弃原地重排 (In-place Reordering)：** 原地重排涉及频繁的删除和插入操作，指针逻辑复杂，且易导致空指针解引用（如你提交的 C 版本）。这种复杂性是**低品味**的体现。
    *   **选择双链表：** 创建两条新的、独立的链表——一条存放 $<x$ 的节点，一条存放 $\geq x$ 的节点。这种方法将复杂的重排操作简化为简单的**尾部追加**操作。

2.  **数据结构和指针策略：**
    *   **哑节点 (Dummy Nodes)：** 必须使用两个哑节点（`lh`, `rh`）作为两条新链表的虚拟头。这确保了无论哪条链表最终是否为空，我们都可以通过 `lh->next` 或 `rh->next` 获取到正确的头部，**消除所有头部特殊情况**。
    *   **尾指针 (Tails)：** 使用两个尾指针（`lt`, `rt`）来跟踪两条链表的当前尾部。这样每次追加节点都是 $O(1)$ 操作。

3.  **操作流程：**
    *   **遍历与追加：** 遍历原始链表，根据节点值直接连接到 `lt->next` 或 `rt->next`。这自然地保持了节点在各自组内的相对顺序。
    *   **合并 (Merge)：** 循环结束后，将 $<x$ 链的尾部 (`lt->next`) 连接到 $\geq x$ 链的头部 (`rh->next`)。
    *   **终止：** 必须将 $\geq x$ 链的尾部 (`rt->next`) 设为 `NULL`，以正确终止新链表。

4.  **C 语言的实用主义要求：**
    *   **内存安全：** 在 C 语言版本中，必须通过 `malloc` 创建哑节点，并在函数返回前，用 `free` 释放这两个临时创建的哑节点 (`lh`, `rh`)。任何资源分配必须伴随资源释放。

**总结：** 双链表法将一个看似困难的链表分区问题，分解为两个简单的尾部插入操作，最后通过 $O(1)$ 的连接步骤完成合并。这是保证代码简洁性、正确性和健壮性的最佳选择。

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

struct ListNode* partition(struct ListNode* head, int x) {
    if (head == NULL) {
        return NULL;
    }

    // 1. 创建两个哑节点 (Dummy Heads)
    // 必须使用 malloc 分配，因为哑节点在函数结束后必须被 free 释放。
    struct ListNode *lh = (struct ListNode*)malloc(sizeof(struct ListNode)); // Less Head
    struct ListNode *rh = (struct ListNode*)malloc(sizeof(struct ListNode)); // Greater Head

    // 防御性编程：检查内存分配
    if (!lh || !rh) {
        if (lh) free(lh);
        if (rh) free(rh);
        return NULL; // 内存分配失败，返回 NULL 或采取其他错误处理
    }

    // 初始化哑节点（值不重要）
    lh->val = 0; 
    lh->next = NULL;
    rh->val = 0;
    rh->next = NULL;

    // lt, rt 充当尾指针 (Tails)
    struct ListNode *lt = lh;
    struct ListNode *rt = rh;
    struct ListNode *p = head;

    // 2. 遍历原链表，进行分区
    while (p != NULL) {
        if (p->val < x) {
            // 连接到小于 x 的链表尾部
            lt->next = p;
            lt = p;
        } else {
            // 连接到大于等于 x 的链表尾部
            rt->next = p;
            rt = p;
        }
        p = p->next;
    }

    // 3. 连接两部分链表
    
    // 小于 x 的链表尾部 (lt) 连接到大于等于 x 的链表头部 (rh->next)
    lt->next = rh->next;
    
    // 确保后半部分的尾部正确终止，避免旧的连接或循环
    rt->next = NULL; 

    // 4. 获取新链表的头，并清理哑节点
    struct ListNode *new_head = lh->next;
    
    // 必须释放通过 malloc 创建的哑节点
    free(lh); 
    free(rh); 

    return new_head;
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
/**
 * 使用双链表法解决分区问题。
 * O(N) 时间，O(1) 额外空间（不计入哑节点）。
 */
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        // 创建两个哑节点 (dummy heads)
        // lh: less head (小于 x 的链表)
        // rh: greater head (大于等于 x 的链表)
        auto lh = new ListNode(-1);
        auto rh = new ListNode(-1);
        
        // lt, rt 充当尾指针 (tails)
        auto lt = lh;
        auto rt = rh;

        auto p = head;
        while (p) {
            // 将节点直接连接到对应的链表尾部
            if (p->val < x) {
                lt->next = p;
                lt = p;
            } else {
                rt->next = p;
                rt = p;
            }
            p = p->next;
        }

        // 1. 连接两部分链表：lt 的尾部连接 rh 的头部
        lt->next = rh->next;
        
        // 2. 确保后半部分的尾部正确终止（避免循环链表或旧的连接）
        rt->next = nullptr; 

        // 3. 获取新链表的头，并清理哑节点
        auto new_head = lh->next;
        delete lh; // 释放 less head
        delete rh; // 释放 greater head

        return new_head;
    }
};
```

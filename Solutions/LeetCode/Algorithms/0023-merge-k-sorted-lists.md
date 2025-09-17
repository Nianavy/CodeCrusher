# Question

[merge-k-sorted-lists](https://leetcode.cn/problems/merge-k-sorted-lists/)



# Answer

> solution

解决“合并 K 个有序链表”问题的核心思路，无论 C++ 还是 C，都集中在如何**高效地从 `k` 个链表的当前头节点中找到最小的那个**。一旦找到最小的，就把它添加到结果链表中，然后把这个最小节点所在的链表的下一个节点（如果存在）放回“候选者”集合中，继续寻找下一个最小值。

### **核心算法思路 (C++ 和 C 共享)**

1.  **虚拟头节点 (Dummy Node)**：
    *   **作用**：与合并两个链表一样，创建一个临时的虚拟头节点 (`dummy`) 来简化代码逻辑，尤其是在处理结果链表的头部时。
    *   **实现**：在栈上创建 `ListNode dummy;` (C++) 或 `struct ListNode dummy = { .val = 0, .next = NULL };` (C)。
    *   **尾指针 (Tail Pointer)**：用一个指针 `tail` 始终指向当前结果链表的末尾，方便 O(1) 添加新节点。

2.  **优先级队列 (Priority Queue / 最小堆)**：
    *   **作用**：这是算法的核心。优先级队列能够高效地（O(log k) 时间复杂度）存储 `k` 个链表的当前头节点，并随时取出其中值最小的那个。
    *   **存储内容**：队列中存储的是 `ListNode*` 指针，代表 `k` 个链表中每个链表的当前最小（头部）节点。
    *   **比较规则**：这是一个**最小堆**，所以比较规则是：值小的节点优先级高（在堆顶）。

3.  **初始化堆**：
    *   遍历输入的 `k` 个链表。
    *   对于每个非空的链表，将其第一个节点（头节点）推入优先级队列中。

4.  **循环处理**：
    *   只要优先级队列不为空，就重复以下步骤：
        *   从队列中**取出**堆顶元素（即所有链表中当前最小的节点）。
        *   将这个最小节点连接到结果链表的 `tail->next`，并更新 `tail` 指针。
        *   检查这个最小节点是否有下一个节点。如果**有**，将这个下一个节点**推入**优先级队列中。这样，该链表的新头部就成为了下一个候选者。

5.  **返回结果**：
    *   循环结束后，虚拟头节点的 `next` 指向的就是合并后的完整有序链表。返回 `dummy.next`。

总结来说，两个版本的核心算法是相同的“多路归并（基于优先级队列）”思想。


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
typedef struct {
    struct ListNode** nodes; // 存储链表节点的指针数组
    int capacity;            // 堆的容量
    int size;                // 堆中当前元素的数量
} MinHeap;

// 初始化最小堆
MinHeap* min_heap_create(int capacity) {
    MinHeap* heap = (MinHeap*)malloc(sizeof(MinHeap));
    if (heap == NULL) {
        fprintf(stderr, "min_heap_create: Failed to allocate heap.\n");
        exit(EXIT_FAILURE);
    }
    heap->nodes = (struct ListNode**)malloc(capacity * sizeof(struct ListNode*));
    if (heap->nodes == NULL) {
        fprintf(stderr, "min_heap_create: Failed to allocate heap nodes array.\n");
        free(heap);
        exit(EXIT_FAILURE);
    }
    heap->capacity = capacity;
    heap->size = 0;
    return heap;
}

// 释放最小堆
void min_heap_destroy(MinHeap* heap) {
    if (heap == NULL) return;
    free(heap->nodes);
    free(heap);
}

// 交换堆中的两个节点
static void swap_nodes(struct ListNode** a, struct ListNode** b) {
    struct ListNode* temp = *a;
    *a = *b;
    *b = temp;
}

// 维护最小堆性质（从父节点向下调整）
static void min_heapify_down(MinHeap* heap, int idx) {
    int smallest = idx;
    int left = 2 * idx + 1;
    int right = 2 * idx + 2;

    if (left < heap->size && heap->nodes[left]->val < heap->nodes[smallest]->val) {
        smallest = left;
    }
    if (right < heap->size && heap->nodes[right]->val < heap->nodes[smallest]->val) {
        smallest = right;
    }

    if (smallest != idx) {
        swap_nodes(&heap->nodes[idx], &heap->nodes[smallest]);
        min_heapify_down(heap, smallest);
    }
}

// 维护最小堆性质（从子节点向上调整）
static void min_heapify_up(MinHeap* heap, int idx) {
    int parent = (idx - 1) / 2;
    while (idx > 0 && heap->nodes[idx]->val < heap->nodes[parent]->val) {
        swap_nodes(&heap->nodes[idx], &heap->nodes[parent]);
        idx = parent;
        parent = (idx - 1) / 2;
    }
}

// 插入一个节点到堆中
void min_heap_push(MinHeap* heap, struct ListNode* node) {
    if (heap->size == heap->capacity) {
        fprintf(stderr, "min_heap_push: Heap is full, cannot push.\n");
        exit(EXIT_FAILURE);
    }
    heap->nodes[heap->size] = node;
    heap->size++;
    min_heapify_up(heap, heap->size - 1); // 向上调整
}

// 从堆中取出最小节点 (堆顶)
struct ListNode* min_heap_pop(MinHeap* heap) {
    if (heap->size == 0) {
        return NULL;
    }
    struct ListNode* root = heap->nodes[0];
    heap->nodes[0] = heap->nodes[heap->size - 1]; // 将最后一个元素移到堆顶
    heap->size--;
    if (heap->size > 0) {
        min_heapify_down(heap, 0); // 向下调整
    }
    return root;
}

// 检查堆是否为空
int min_heap_is_empty(MinHeap* heap) {
    return heap->size == 0;
}

// ----------------------------------------------------------
// mergeKLists 函数实现
// ----------------------------------------------------------

struct ListNode* mergeKLists(struct ListNode** lists, int listsSize) {
    // 创建一个栈上的虚拟头节点
    struct ListNode dummy = { .val = 0, .next = NULL };
    struct ListNode* tail = &dummy;

    // 创建一个最小堆，容量为 k (listsSize)
    MinHeap* heap = min_heap_create(listsSize);
    if (heap == NULL) { // 再次检查内存分配失败
        return NULL;
    }

    // 将所有链表的第一个节点推入堆中
    for (int i = 0; i < listsSize; i++) {
        if (lists[i] != NULL) {
            min_heap_push(heap, lists[i]);
        }
    }

    // 当堆不为空时，不断取出最小值
    while (!min_heap_is_empty(heap)) {
        struct ListNode* smallest_node = min_heap_pop(heap);

        // 连接到合并链表的末尾
        tail->next = smallest_node;
        tail = smallest_node;

        // 如果取出的节点还有下一个节点，则将其推入堆中
        if (smallest_node->next != NULL) {
            min_heap_push(heap, smallest_node->next);
        }
    }

    // 释放堆结构本身占用的内存
    min_heap_destroy(heap);

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
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        auto cmp = [](ListNode *a, ListNode *b) { return a->val > b->val; };
        priority_queue<ListNode *, vector<ListNode *>, decltype(cmp) > heap(cmp);
        ListNode dummy;
        ListNode *tail = &dummy;
        for (ListNode *list : lists) {
            if (list != nullptr) heap.push(list);
        }
        while (!heap.empty()) {
            ListNode *smallest_node = heap.top();
            heap.pop();

            tail->next = smallest_node;
            tail = smallest_node;

            if (smallest_node->next != nullptr) {
                heap.push(smallest_node->next);
            }
        }
        return dummy.next;
    }
};
```

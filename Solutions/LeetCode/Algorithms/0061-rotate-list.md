# Question

[rotate-list](https://leetcode.cn/problems/rotate-list/)



# Answer

> solution

### **解决思路总结：闭环-断开法**

**格言：** “链表操作应该像外科手术一样精确。不要添加不必要的工具（如虚拟头节点），直接找到切口，缝合，再断开。”

**核心思想：**
将链表旋转 $K$ 次，等价于将链表的后 $K_{eff}$ 个节点移动到链表头部，其中 $K_{eff} = K \bmod N$（$N$ 为链表长度）。实现 O(N) 的目标，需要两步遍历：一次找到长度和尾巴，一次找到断裂点。

1.  **预处理：长度、尾巴与有效旋转次数 (O(N))**
    *   **遍历链表**：计算链表总长度 $N$，并同时找到链表的尾节点 `tail`。
    *   **优化 $K$**：计算有效的旋转次数 $K_{eff} = K \bmod N$。
    *   **特殊情况**：如果 $K_{eff} = 0$，表示无需旋转，直接返回原 `head`。

2.  **闭合成环 (O(1))**
    *   为了方便旋转，我们将链表从逻辑上闭合成一个环：将 `tail` 节点的 `next` 指向原始的 `head` 节点。
    *   `tail->next = head;`

3.  **定位断裂点 (O(N))**
    *   旋转 $K_{eff}$ 次，意味着原链表的倒数第 $K_{eff}$ 个节点将成为新链表的头部。
    *   这等同于从 `head` 开始，向前移动 $(N - K_{eff})$ 步到达新尾巴的位置。
    *   我们需要找到**新尾巴的前一个节点**（即断裂点的前一个节点），这个节点就是从原 `head` 移动 $(N - K_{eff} - 1)$ 步到达的位置 `new_tail_prev`。
    *   我们使用一个指针从 `head` 开始，精确移动 $(N - K_{eff} - 1)$ 步到达 `new_tail_prev`。

4.  **最终断开与重置头部 (O(1))**
    *   **设置新头：** 新的头部是 `new_tail_prev->next`。
    *   **断开环：** 将 `new_tail_prev->next` 设置为 `NULL` (或 `nullptr`)，从而完成断开环的操作，将这个节点设为新链表的尾巴。

**简洁执念的体现：**
该方法通过巧妙地将链表转化为一个环，避免了复杂的指针连接和边界检查，然后用精确计算的步数找到断裂点并“剪断”环路。整个过程仅需 O(1) 额外空间，且两次线性遍历，是最符合高效、简洁原则的解决方案。

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
struct ListNode* rotateRight(struct ListNode* head, int k) {
    // 1. 特殊情况处理
    if (head == NULL || k == 0) {
        return head;
    }

    // 2. 遍历计算长度 (n) 并找到尾节点 (tail)
    int n = 1; 
    struct ListNode* tail = head;
    while (tail->next != NULL) {
        tail = tail->next;
        n++;
    }

    // 3. 计算有效的旋转次数 (k %= n)
    k %= n;
    
    // 如果 k 为 0，无需旋转
    if (k == 0) {
        return head;
    }

    // 4. 找到新的尾节点的前一个节点 (断裂点的前一个节点)
    // 需要移动 n - k - 1 步
    struct ListNode* new_tail_prev = head;
    for (int i = 0; i < n - k - 1; ++i) {
        new_tail_prev = new_tail_prev->next;
    }

    // 5. 执行旋转操作 (三步)
    // a. 闭环：将旧尾连接到旧头
    tail->next = head;

    // b. 设置新头：新头是断裂点
    head = new_tail_prev->next;

    // c. 断开环：设置新尾的 next 为 NULL
    new_tail_prev->next = NULL;
    
    return head;
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
 *     ListNode(int x, ListNode *next) : val(x, next) {}
 * };
 */
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        // 1. 特殊情况处理
        if (head == nullptr || k == 0) {
            return head;
        }

        // 2. 遍历计算长度 (n) 并找到尾节点 (tail)
        int n = 1; // 至少有一个节点
        ListNode* tail = head;
        while (tail->next != nullptr) {
            tail = tail->next;
            n++;
        }

        // 3. 计算有效的旋转次数 (k %= n)
        k %= n;
        
        // 如果 k 为 0，无需旋转，直接返回原头
        if (k == 0) {
            return head;
        }

        // 4. 找到新的尾节点 (即断裂点的前一个节点)
        // 新的尾巴距离旧头 n - k 个节点，即移动 n - k - 1 步
        ListNode* new_tail_prev = head;
        for (int i = 0; i < n - k - 1; ++i) {
            new_tail_prev = new_tail_prev->next;
        }

        // 5. 执行旋转操作 (三步)
        // a. 闭环：将旧尾连接到旧头
        tail->next = head;

        // b. 设置新头：新头是断裂点
        head = new_tail_prev->next;

        // c. 断开环：设置新尾的 next 为 nullptr
        new_tail_prev->next = nullptr;
        
        return head;
    }
};
```

# Question

[insertion-sort-list](https://leetcode.cn/problems/insertion-sort-list/)



# Answer

> solution

链表插入排序 原理与实现总结
核心思想
将未排序部分逐个插入已排序部分，从前往后扫描，找到合适位置插入。

算法步骤
创建 dummy 节点：用于简化边界处理，首插入点固定
遍历链表：从头开始，逐个处理节点
寻找插入位置：从 dummy 开始，在已排序部分寻找
插入节点：更新指针，将节点插入找到位置
继续下一节点：处理原链表的下一个节点
时间复杂度
最坏情况：O(n²) - 数据已逆序，每次都要从头找位置
平均情况：O(n²) - 需要遍历已排序部分
空间复杂度：O(1) - 只使用常数额外空间
适用场景
适合：小数据量（<10K）、教学演示、面试算法讨论
不适合：生产环境、大数据量（>100K）、高性能要求
优缺点
优点：

代码简单，易于理解
空间复杂度优秀（O(1)）
能保证稳定排序
缺点：

时间复杂度差，大数据超时
效率低，不适合实际应用
为什么链表插入排序比数组慢？
链表无法随机访问，必须线性遍历，导致找位置代价高。数组可以用二分查找加速，但链表不行。

### Way Of C

> source code

```c
// 归并排序
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
// 合并两个有序链表
static struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode dummy;
    struct ListNode *tail = &dummy;
    
    while (l1 && l2) {
        if (l1->val <= l2->val) {
            tail->next = l1;
            l1 = l1->next;
        } else {
            tail->next = l2;
            l2 = l2->next;
        }
        tail = tail->next;
    }
    
    tail->next = (l1) ? l1 : l2;
    return dummy.next;
}
// 寻找链表中点（快慢指针）
static struct ListNode* getMid(struct ListNode* head) {
    if (!head || !head->next) return NULL;
    
    struct ListNode *slow = head;
    struct ListNode *fast = head->next;
    
    while (fast && fast->next) {
        fast = fast->next->next;
        slow = slow->next;
    }
    
    struct ListNode *mid = slow->next;
    slow->next = NULL; // 断开链表
    return mid;
}
// 递归归并排序
static struct ListNode* mergeSortList(struct ListNode* head) {
    if (!head || !head->next) return head;
    
    // 找到中点并断开
    struct ListNode *mid = getMid(head);
    
    // 递归排序左右两半
    struct ListNode *left = mergeSortList(head);
    struct ListNode *right = mergeSortList(mid);
    
    // 合并两个有序链表
    return mergeTwoLists(left, right);
}
// 插入排序的替代实现：使用归并排序
struct ListNode* insertionSortList(struct ListNode* head) {
    // 面试或算法题目中，即使叫插入排序，
    // 如果插入排序超时，说明数据规模大，
    // 必须使用归并排序才能通过
    return mergeSortList(head);
}
// 辅助函数：打印链表
void printList(struct ListNode* head) {
    while (head) {
        printf("%d ", head->val);
        head = head->next;
    }
    printf("\n");
}
// 辅助函数：释放链表
void freeList(struct ListNode *head) {
    while (head) {
        struct ListNode *next = head->next;
        free(head);
        head = next;
    }
}

// 插入排序
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode *insertionSortList(struct ListNode *head) {
    if (head == NULL) {
        return head;
    }
    struct ListNode *dummyHead = malloc(sizeof(struct ListNode));
    dummyHead->val = 0;
    dummyHead->next = head;
    struct ListNode *lastSorted = head;
    struct ListNode *curr = head->next;
    while (curr != NULL) {
        if (lastSorted->val <= curr->val) {
            lastSorted = lastSorted->next;
        } else {
            struct ListNode *prev = dummyHead;
            while (prev->next->val <= curr->val) {
                prev = prev->next;
            }
            lastSorted->next = curr->next;
            curr->next = prev->next;
            prev->next = curr;
        }
        curr = lastSorted->next;
    }
    return dummyHead->next;
}
```

---

### Way Of C++

> source code

```c++
// 归并排序
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
    ListNode* insertionSortList(ListNode* head) {
        // 实际调用归并排序
        return mergeSortList(head);
    }
    
private:
    // 归并排序
    ListNode* mergeSortList(ListNode* head) {
        if (!head || !head->next) return head;
        
        ListNode* slow = head;
        ListNode* fast = head->next;
        
        // 找到中点
        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
        }
        
        ListNode* mid = slow->next;
        slow->next = nullptr; // 断开链表
        
        ListNode* left = mergeSortList(head);
        ListNode* right = mergeSortList(mid);
        
        return merge(left, right);
    }
    
    // 合并两个有序链表
    ListNode* merge(ListNode* l1, ListNode* l2) {
        ListNode dummy(0);
        ListNode* tail = &dummy;
        
        while (l1 && l2) {
            if (l1->val <= l2->val) {
                tail->next = l1;
                l1 = l1->next;
            } else {
                tail->next = l2;
                l2 = l2->next;
            }
            tail = tail->next;
        }
        
        tail->next = (l1) ? l1 : l2;
        return dummy.next;
    }
};


// 插入排序


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
    ListNode* insertionSortList(ListNode* head) {
        if (!head) return nullptr;
        
        auto dummy = new ListNode(-1);
        auto curr = head;
        
        while (curr) {
            // 保存下一个要处理的节点
            auto next = curr->next;
            
            // 从 dummy 开始寻找插入位置
            auto prev = dummy;
            while (prev->next && prev->next->val <= curr->val) {
                prev = prev->next;
            }
            
            // 插入节点
            curr->next = prev->next;
            prev->next = curr;
            
            // 继续处理下一个节点
            curr = next;
        }
        
        auto sorted = dummy->next;
        delete dummy;
        return sorted;
    }
};
```

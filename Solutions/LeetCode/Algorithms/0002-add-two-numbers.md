# Question

[add-two-numbers](https://leetcode.cn/problems/add-two-numbers/)



# Answer

### Way Of C

> think

1.  **目的：** 创建一个新的链表来表示两个输入数字的和，不修改任何输入。
2.  **方法：**
    *   使用一个 **哑节点 (dummy node)** 作为结果链表的起点，避免处理头节点的特殊情况。
    *   使用一个 **当前节点指针 (current pointer)** 始终指向新链表的末尾，便于追加新节点。
    *   采用 **逐位相加** 的策略，从个位开始，向高位移动。
    *   **维护一个 `carry` (进位)** 变量，将当前位的进位传递给下一位。
    *   使用 **统一的循环条件 `(l1 != NULL || l2 != NULL || carry != 0)`** 来处理链表长度不一致和最终进位的所有情况。
    *   每次循环都 **分配一个新的节点** 来存储当前位的计算结果。

> source code

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* addTwoNumbers(struct  ListNode* l1, struct  ListNode* l2) {
    int carry = 0;
    struct ListNode dummy;
    struct ListNode *current = &dummy;
    dummy.next = NULL;

    while (l1 != NULL || l2 != NULL || carry != 0) {
        int sum = carry;

        if (l1 != NULL) {
            sum += l1->val;
            l1 = l1->next;
        }
        if (l2 != NULL) {
            sum += l2->val;
            l2 = l2->next;
        }

        struct ListNode *newNode = malloc(sizeof(struct ListNode));
        if (newNode == NULL) {
            fprintf(stderr, "Memory allocation failed!\n");
            exit(EXIT_FAILURE);
        }
        newNode->val = sum % 10;
        newNode->next = NULL;

        current->next = newNode;
        current = newNode;

        carry = sum / 10;
    }
    return dummy.next;
}
```

---


### Way Of C++

> think

1.  **目的：** 创建一个新的链表来表示两个输入数字的和，不修改任何输入。
2.  **方法：**
    *   使用一个 **哑节点 (dummy node)** 作为结果链表的起点，避免处理头节点的特殊情况。
    *   使用一个 **当前节点指针 (current pointer)** 始终指向新链表的末尾，便于追加新节点。
    *   采用 **逐位相加** 的策略，从个位开始，向高位移动。
    *   **维护一个 `carry` (进位)** 变量，将当前位的进位传递给下一位。
    *   使用 **统一的循环条件 `(l1 != nullptr || l2 != nullptr || carry != 0)`** 来处理链表长度不一致和最终进位的所有情况。
    *   每次循环都 **分配一个新的节点** 来存储当前位的计算结果。

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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int carry = 0;
        ListNode dummy;
        ListNode *current = &dummy;

        while (l1 != nullptr || l2 != nullptr || carry != 0) {
            int sum = carry;

            if (l1 != nullptr) {
                sum += l1->val;
                l1 = l1->next;
            }
            if (l2 != nullptr) {
                sum += l2->val;
                l2 = l2->next;
            }

            current->next = new ListNode(sum % 10); 
            current = current->next;

            carry = sum / 10;
        }
        
        return dummy.next;
    }
};
```

# Question

[](https://leetcode.cn/problems//)



# Answer

### Way Of C

> think

> source code

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct ListNode {
    int val;
    struct ListNode *next;
} ListNode;

ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    int carry = 0;
    ListNode dummy;
    ListNode *p = l1, *prev = &dummy;
    dummy.next = p;
    while (l1 != NULL || l2 != NULL) {
        int sum = 0;
        if (l1 != NULL) {
            sum += l1->val;
            l1 = l1->next;
        }
        if (l2 != NULL) {
            if (p == NULL) {
                prev->next = l2;
                p = l2;
            }
            sum += l2->val;
            l2 = l2->next;
        }
        sum += carry;
        carry = sum / 10;
        p->val = sum % 10;
        prev = p;
        p = p->next;
    }
    if (carry) {
        p = malloc(sizeof(*p));
        p->val = carry;
        p->next = NULL;
        prev->next = p;
    }
    return dummy.next;
}

static ListNode *node_build(const char *digits) {
    ListNode *res, *p, *prev;
    int first = 1;
    int len = strlen(digits);
    const char *c = digits + len - 1;
    prev = NULL;
    while (len-- > 0) {
        p = malloc(sizeof(*p));
        if (first) {
            first = 0;
            res = p;
        }
        p->val = *c-- - '0';
        p->next = NULL;
        if (prev != NULL) prev->next = p;
        prev = p;
    }
    return res;
}

static void show(ListNode *ln) {
    int sum = 0, factor = 1;
    while (ln != NULL) {
        sum += ln->val * factor;
        factor *= 10;
        ln = ln->next;
    }
    printf("%d\n", sum);
}

int main(int argc, char *argv[]) {
    if (argc < 3) {
        fprintf(stderr, "Usage: ./test n1 n2\n");
        exit(-1);
    }
    ListNode *l1 = node_build(argv[1]);
    ListNode *l2 = node_build(argv[2]);
    ListNode *res = addTwoNumbers(l1, l2);
    show(l1), show(l2), show(res);
    return 0;
}
```

---


### Way Of C++

> think



> source code

```c++

```

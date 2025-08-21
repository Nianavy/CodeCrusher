https://www.hackerrank.com/challenges/simple-text-editor

```txt
Use a stack to save the string length before edits,
enabling undo for append and delete operations — provided the original characters are not overwritten.
```

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

typedef struct {
    char** history;
    int top;
    int capacity; 
} UndoStack;

void initStack(UndoStack* stack, int initialCapacity) {
    stack->capacity = initialCapacity;
    stack->history = (char**)malloc(stack->capacity * sizeof(char*));
    if (stack->history == NULL) {
        exit(EXIT_FAILURE);
    }
    stack->top = -1;
}

void push(UndoStack* stack, const char* s) {
    if (stack->top + 1 >= stack->capacity) {
        stack->capacity *= 2;
        stack->history = (char**)realloc(stack->history, stack->capacity * sizeof(char*));
        if (stack->history == NULL) {
            exit(EXIT_FAILURE);
        }
    }
    char* s_copy = strdup(s);
    if (s_copy == NULL) {
        exit(EXIT_FAILURE);
    }
    stack->history[++(stack->top)] = s_copy;
}

char* pop(UndoStack* stack) {
    if (stack->top == -1) {
        return NULL;
    }
    char* s_copy = stack->history[(stack->top)--];
    return s_copy;
}

void freeStack(UndoStack* stack) {
    while (stack->top != -1) {
        free(pop(stack));
    }
    free(stack->history);
}

int main() {
    int Q;
    scanf("%d", &Q);

    char* S = (char*)calloc(1, sizeof(char));
    if (S == NULL) {
        return 1;
    }
    S[0] = '\0';

    UndoStack undoStack;
    initStack(&undoStack, 10);

    for (int i = 0; i < Q; ++i) {
        int type;
        scanf("%d", &type);

        if (type == 1) {
            char W[100001];
            scanf("%s", W);
            push(&undoStack, S);
            size_t current_len = strlen(S);
            size_t w_len = strlen(W);
            S = (char*)realloc(S, (current_len + w_len + 1) * sizeof(char));
            if (S == NULL) {
                exit(EXIT_FAILURE);
            }
            strcat(S, W);
        } else if (type == 2) {
            int k;
            scanf("%d", &k);
            push(&undoStack, S);
            size_t current_len = strlen(S);
            if (k > current_len) {
                k = current_len;
            }
            S[current_len - k] = '\0';
        } else if (type == 3) {
            int k;
            scanf("%d", &k);
            printf("%c\n", S[k - 1]); 
        } else if (type == 4) {
            char* prev_S = pop(&undoStack);
            if (prev_S != NULL) {
                free(S);
                S = prev_S;
            }
        }
    }

    free(S);
    freeStack(&undoStack);

    return 0;
}
```

https://www.hackerrank.com/challenges/and-xor-or

```txt
using two monotonic stacks to find the indices of the first smaller elements
to the left and right of each array element,
then calculating the maximum XOR value from these pairs.
```

```c
#include <assert.h>
#include <ctype.h>
#include <limits.h>
#include <math.h>
#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char* readline();
char* ltrim(char*);
char* rtrim(char*);
char** split_string(char*);

int parse_int(char*);

/*
 * Complete the 'andXorOr' function below.
 *
 * The function is expected to return an INTEGER.
 * The function accepts INTEGER_ARRAY a as parameter.
 */
 
typedef struct {
    int* data;
    int top;
    int capacity;
} C_Stack;

void stack_init(C_Stack* s, int capacity) {
    s->capacity = capacity;
    s->data = (int*)malloc(capacity * sizeof(int));
    if (s->data == NULL) {
        exit(EXIT_FAILURE);
    }
    s->top = -1;
}

bool stack_empty(const C_Stack* s) {
    return s->top == -1;
}

void stack_push(C_Stack* s, int val) {
    s->top++;
    s->data[s->top] = val;
}

int stack_top_val(const C_Stack* s) {
    return s->data[s->top];
}

int stack_pop(C_Stack* s) {
    int val = s->data[s->top];
    s->top--;
    return val;
}

void stack_free(C_Stack* s) {
    free(s->data);
    s->data = NULL;
    s->top = -1;
    s->capacity = 0;
}

int max_int(int a, int b) {
    return (a > b) ? a : b;
}

int andXorOr(int a_count, int* a) {
    int maxXOR = 0;

    int* prevSmaller = (int*)malloc(a_count * sizeof(int));
    int* nextSmaller = (int*)malloc(a_count * sizeof(int));
    if (prevSmaller == NULL || nextSmaller == NULL) {
        if (prevSmaller) free(prevSmaller);
        if (nextSmaller) free(nextSmaller);
        exit(EXIT_FAILURE);
    }

    for (int i = 0; i < a_count; ++i) {
        prevSmaller[i] = -1;
        nextSmaller[i] = a_count;
    }

    C_Stack prev_stack;
    C_Stack next_stack;
    stack_init(&prev_stack, a_count);
    stack_init(&next_stack, a_count);

    stack_push(&prev_stack, 0);
    for (int i = 1; i < a_count; ++i) {
        while (!stack_empty(&prev_stack) && a[stack_top_val(&prev_stack)] >= a[i]) {
            stack_pop(&prev_stack);
        }
        if (!stack_empty(&prev_stack)) {
            prevSmaller[i] = stack_top_val(&prev_stack);
        }
        stack_push(&prev_stack, i);
    }
    stack_free(&prev_stack);

    stack_push(&next_stack, a_count - 1);
    for (int i = a_count - 2; i >= 0; --i) {
        while (!stack_empty(&next_stack) && a[i] <= a[stack_top_val(&next_stack)]) {
            stack_pop(&next_stack);
        }
        if (!stack_empty(&next_stack)) {
            nextSmaller[i] = stack_top_val(&next_stack);
        }
        stack_push(&next_stack, i);
    }
    stack_free(&next_stack);

    for (int i = 0; i < a_count; ++i) {
        if (prevSmaller[i] != -1) {
            maxXOR = max_int(maxXOR, a[i] ^ a[prevSmaller[i]]);
        }
        if (nextSmaller[i] != a_count) {
            maxXOR = max_int(maxXOR, a[i] ^ a[nextSmaller[i]]);
        }
    }

    free(prevSmaller);
    free(nextSmaller);

    return maxXOR;
}

int main()
{
    FILE* fptr = fopen(getenv("OUTPUT_PATH"), "w");

    int a_count = parse_int(ltrim(rtrim(readline())));

    char** a_temp = split_string(rtrim(readline()));

    int* a = malloc(a_count * sizeof(int));

    for (int i = 0; i < a_count; i++) {
        int a_item = parse_int(*(a_temp + i));

        *(a + i) = a_item;
    }

    int result = andXorOr(a_count, a);

    fprintf(fptr, "%d\n", result);

    fclose(fptr);

    return 0;
}

char* readline() {
    size_t alloc_length = 1024;
    size_t data_length = 0;

    char* data = malloc(alloc_length);

    while (true) {
        char* cursor = data + data_length;
        char* line = fgets(cursor, alloc_length - data_length, stdin);

        if (!line) {
            break;
        }

        data_length += strlen(cursor);

        if (data_length < alloc_length - 1 || data[data_length - 1] == '\n') {
            break;
        }

        alloc_length <<= 1;

        data = realloc(data, alloc_length);

        if (!data) {
            data = '\0';

            break;
        }
    }

    if (data[data_length - 1] == '\n') {
        data[data_length - 1] = '\0';

        data = realloc(data, data_length);

        if (!data) {
            data = '\0';
        }
    } else {
        data = realloc(data, data_length + 1);

        if (!data) {
            data = '\0';
        } else {
            data[data_length] = '\0';
        }
    }

    return data;
}

char* ltrim(char* str) {
    if (!str) {
        return '\0';
    }

    if (!*str) {
        return str;
    }

    while (*str != '\0' && isspace(*str)) {
        str++;
    }

    return str;
}

char* rtrim(char* str) {
    if (!str) {
        return '\0';
    }

    if (!*str) {
        return str;
    }

    char* end = str + strlen(str) - 1;

    while (end >= str && isspace(*end)) {
        end--;
    }

    *(end + 1) = '\0';

    return str;
}

char** split_string(char* str) {
    char** splits = NULL;
    char* token = strtok(str, " ");

    int spaces = 0;

    while (token) {
        splits = realloc(splits, sizeof(char*) * ++spaces);

        if (!splits) {
            return splits;
        }

        splits[spaces - 1] = token;

        token = strtok(NULL, " ");
    }

    return splits;
}

int parse_int(char* str) {
    char* endptr;
    int value = strtol(str, &endptr, 10);

    if (endptr == str || *endptr != '\0') {
        exit(EXIT_FAILURE);
    }

    return value;
}
```

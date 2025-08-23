https://www.hackerrank.com/challenges/poisonous-plants

```txt
Use a monotonic stack to track survival days of plants and update the maximum death day dynamically. 
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
 * Complete the 'poisonousPlants' function below.
 *
 * The function is expected to return an INTEGER.
 * The function accepts INTEGER_ARRAY p as parameter.
 */

int max(int a, int b) {
    return (a > b) ? a : b;
}

int poisonousPlants(int p_count, int* p) {
    int* days_to_live = calloc(p_count, sizeof(int));
    int* stack = malloc(p_count * sizeof(int));
    int stack_top = -1;

    int max_overall_days = 0;

    for (int i = 0; i < p_count; ++i) {
        int current_plant_max_days_from_left = 0;
        while (stack_top != -1 && p[stack[stack_top]] >= p[i]) {
            current_plant_max_days_from_left = max(current_plant_max_days_from_left, days_to_live[stack[stack_top]]);
            stack_top--;
        }

        if (stack_top == -1) days_to_live[i] = 0;
        else days_to_live[i] = current_plant_max_days_from_left + 1;
        
        max_overall_days = max(max_overall_days, days_to_live[i]);

        stack_top++;
        stack[stack_top] = i;
    }

    free(days_to_live);
    free(stack);

    return max_overall_days;
}

int main()
{
    FILE* fptr = fopen(getenv("OUTPUT_PATH"), "w");

    int n = parse_int(ltrim(rtrim(readline())));

    char** p_temp = split_string(rtrim(readline()));

    int* p = malloc(n * sizeof(int));

    for (int i = 0; i < n; i++) {
        int p_item = parse_int(*(p_temp + i));

        *(p + i) = p_item;
    }

    int result = poisonousPlants(n, p);

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

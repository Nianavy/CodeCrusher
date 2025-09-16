# Question

[generate-parentheses](https://leetcode.cn/problems/generate-parentheses/)



# Answer

> solution

核心思路：
1.  **状态定义**：
    *   `n`：总共需要生成的括号对数量。
    *   `left_count` (`lc` 或 `l`)：当前已放置的左括号数量。
    *   `right_count` (`rc` 或 `r`)：当前已放置的右括号数量。
    *   `current_sequence` (`seq` 或 `stack`)：当前正在构建的括号序列。
    *   `results` (`ans` 或 `res` 或 `parentheses`)：存储所有合法最终序列的容器。

2.  **递归函数签名**：
    `dfs(n, left_count, right_count, current_sequence, results)`。
    所有必要的状态都作为参数传递，确保函数是**无副作用**的。

3.  **终止条件 (Base Case)**：
    当 `left_count == n` 并且 `right_count == n` 时，说明已经生成了一个包含 `n` 对合法括号的序列。此时，将 `current_sequence` 添加到 `results` 中。

4.  **递归步骤 (Recursive Steps)**：
    有两种可能的选择：添加一个左括号或添加一个右括号。

    *   **添加左括号**：
        *   **条件**：只有当 `left_count < n` (即左括号数量还没用完) 时，才能添加左括号。
        *   **操作**：将 `'('` 添加到 `current_sequence`，然后递归调用 `dfs(n, left_count + 1, right_count, ...)`。
        *   **回溯 (Undo)**：递归调用返回后，**撤销**这个选择，从 `current_sequence` 中移除 `'('`。

    *   **添加右括号**：
        *   **条件**：只有当 `right_count < left_count` (即右括号数量少于左括号数量，以保证合法性) 时，才能添加右括号。
        *   **操作**：将 `')'` 添加到 `current_sequence`，然后递归调用 `dfs(n, left_count, right_count + 1, ...)`。
        *   **回溯 (Undo)**：递归调用返回后，**撤销**这个选择，从 `current_sequence` 中移除 `')'`。

### Way Of C

> source code

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
long long calculate_catalan(int n) {
    if (n == 0) return 1;
    long long res = 1;
    for (int i = 0; i < n; ++i) {
        res = res * (2 * n - i);
        res = res / (i + 1);
    }
    return res / (n + 1);
}

void dfs(int n, int left_count, int right_count, char *current_sequence, int len, char **results, int *count) {
    if (len == 2 * n) {
        current_sequence[len] = '\0';
        results[*count] = (char*)malloc((len + 1) * sizeof(char));
        if (results[*count] == NULL) {
            fprintf(stderr, "dfs: Failed to allocate memory for string.\n");
            exit(EXIT_FAILURE);
        }
        strcpy(results[*count], current_sequence);
        (*count)++;
        return;
    }
    if (left_count < n) {
        current_sequence[len] = '(';
        dfs(n, left_count + 1, right_count, current_sequence, len + 1, results, count);
    }
    if (right_count < left_count) {
        current_sequence[len] = ')';
        dfs(n, left_count, right_count + 1, current_sequence, len + 1, results, count);
    }
}

char** generateParenthesis(int n, int* returnSize) {
    long long num_results = calculate_catalan(n);
    if (num_results > 10000000) {
        fprintf(stderr, "ERR: %lld.\n", num_results);
        exit(EXIT_FAILURE);
    }

    char **results = (char **)malloc(num_results * sizeof(char *));
    if (results == NULL) {
        fprintf(stderr, "ERR.\n");
        exit(EXIT_FAILURE);
    }

    char *current_sequence_buffer = (char *)malloc((2 * n + 1) * sizeof(char));
    if (current_sequence_buffer == NULL) {
        fprintf(stderr, "ERR.\n");
        free(results);
        exit(EXIT_FAILURE);
    }

    *returnSize = 0;
    dfs(n, 0, 0, current_sequence_buffer, 0, results, returnSize);
    free(current_sequence_buffer);
    return results;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
    void dfs(int n, int left_count, int right_count, string &current_sequence, vector<string> &results) {
        if (left_count == n && right_count == n) {
            results.push_back(current_sequence);
            return;
        }

        if (left_count < n) {
            current_sequence.push_back('(');
            dfs(n, left_count + 1, right_count, current_sequence, results);
            current_sequence.pop_back();
        }

        if (right_count < left_count) {
            current_sequence.push_back(')');
            dfs(n, left_count, right_count + 1, current_sequence, results);
            current_sequence.pop_back();
        }
    }
    
public:
    vector<string> generateParenthesis(int n) {
        vector<string> results;
        string current_seq;
        dfs(n, 0, 0, current_seq, results);
        return results;
    }
};
```

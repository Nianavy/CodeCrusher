# Question

[letter-combinations-of-a-phone-number](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)



# Answer

> solution

1.  **映射：** 首先，你需要一个明确的映射，将数字（输入）转换为其对应的字母集合。这是问题的基础数据。
    *   （C++/C）`strs` / `digit_map` 数组：存储数字到字符串的映射。

2.  **递归与深度优先搜索（DFS）：** 组合问题天然适合递归。每个递归调用处理一个数字，并尝试该数字对应的所有可能字母。
    *   （C++/C）`dfs` / `dfs_c` 函数：这是核心的递归函数。

3.  **状态管理（当前路径）：** 在递归过程中，你需要一个变量来跟踪当前已经构建的字母组合路径。
    *   （C++）`std::string current_path`：使用 `push_back()` 添加字母，`pop_back()` 回溯移除字母。
    *   （C）`char *current_path` (或 `char[]`)：使用 `current_path[path_len] = letter` 添加字母，通过 `path_len` 参数的递增和回溯时自动覆盖实现“原地修改”的效果。

4.  **递归终止条件（基本情况）：** 当路径的长度与输入数字字符串的长度相等时，表示一个完整的组合已经形成。
    *   （C++/C）`u == digits.size()` / `u == digits_len`：此时将 `current_path` 添加到最终结果集。
        *   （C）注意在添加前确保 `current_path` 以 `\0` 结尾，并进行深拷贝 (`malloc` + `strcpy`)。

5.  **探索与回溯：** 这是回溯法的精髓。
    *   **探索：** 对于当前数字，遍历其所有可能的字母。将每个字母添加到 `current_path` 中，然后递归调用处理下一个数字。
    *   **回溯：** 递归调用返回后，需要撤销当前字母的添加，以便尝试当前数字的下一个字母。
        *   （C++）`current_path.pop_back()`：显式移除。
        *   （C）由于 `current_path[path_len] = letter` 只是修改了数组的一个位置，当 `path_len` 减少时，这个位置会在下次递归时被自然覆盖，所以无需显式“pop”。

6.  **结果收集：** 需要一个动态的数据结构来收集所有生成的组合字符串。
    *   （C++）`std::vector<std::string> result`：自动管理内存和大小。
    *   （C）`char **results`、`int *num_results`、`int *max_results_capacity`：手动实现动态数组（使用 `realloc` 增长）。

7.  **边界条件：** 处理输入数字字符串为空的情况。

**总结来说，核心思路是：将问题分解为子问题（处理每个数字），通过递归来探索所有子问题的解决方案，并通过状态（当前路径）和回溯机制来有效管理和生成所有可能的组合。**

### Way Of C

> source code

```c
const char *digit_map[] = {
    "", "", "abc", "def", "ghi", "jkl", "mno",
    "pqrs", "tuv", "wxyz"
};

void dfs_c(const char *digits, int digits_len, int u, char *current_path,
        int path_len, char ***results, int *num_results, int *max_results_capacity) {
        if (u == digits_len) {
            current_path[path_len] = '\0';
            char *final_combination = malloc(path_len + 1);
            if (final_combination == NULL) {
                fprintf(stderr, "Err");
                exit(EXIT_FAILURE);
            }
            strcpy(final_combination, current_path);

            if (*num_results >= *max_results_capacity) {
                *max_results_capacity = (*max_results_capacity == 0) ? 1 : (*max_results_capacity * 2);
                *results = realloc(*results, sizeof(char *) * (*max_results_capacity));
                if (*results == NULL) {
                    fprintf(stderr, "Err");
                    exit(EXIT_FAILURE);
                }
            }
            (*results)[(*num_results)++] = final_combination;
            return;
        }

        int digit_val = digits[u] - '0';
        const char *letters = digit_map[digit_val];
        int letters_len = strlen(letters);

        for (int i = 0; i < letters_len; ++i) {
            current_path[path_len] = letters[i];
            dfs_c(digits, digits_len, u + 1, current_path, path_len + 1, results, num_results, max_results_capacity);
        }
}

char **letterCombinations(char *digits, int *returnSize) {
    int digits_len = (digits == NULL) ? 0 : strlen(digits);

    if (digits_len == 0) {
        *returnSize = 0;
        return NULL;
    }

    char **results = NULL;
    int num_results = 0;
    int max_results_capacity = 0;

    char *current_path = malloc(digits_len + 1);
    if (current_path == NULL) {
        fprintf(stderr, "Err");
        exit(EXIT_FAILURE);
    }

    dfs_c(digits, digits_len, 0, current_path, 0, &results, &num_results, &max_results_capacity);

    free(current_path);
    *returnSize = num_results;
    return results;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    string strs[10] = {
        "", "", "abc", "def",
        "ghi", "jkl", "mno",
        "pqrs", "tuv", "wxyz",
    };

    vector<string> letterCombinations(string digits) {
        vector<string> result;
        if (digits.empty()) return result;
        string current_path;
        dfs(digits, 0, current_path, result);
        return result;
    }


    void dfs(const string &digits, int u, string &current_path, vector<string> &result) {
        if (u == digits.size()) result.push_back(current_path);
        else {
            for (char c: strs[digits[u] - '0']) {
                current_path.push_back(c);
                dfs(digits, u + 1, current_path, result);
                current_path.pop_back();
            }
        }
    }
};
```

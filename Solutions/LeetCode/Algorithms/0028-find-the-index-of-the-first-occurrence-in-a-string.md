# Question

[find-the-index-of-the-first-occurrence-in-a-string](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)



# Answer

> solution

KMP (Knuth-Morris-Pratt) 算法解决字符串匹配问题的核心在于：**当匹配失败时，它不会简单地将模式串向右滑动一位然后从头开始比较，而是利用模式串自身的结构信息（即已匹配部分的前缀和后缀关系）来计算模式串应该跳过多少位，从而避免不必要的比较。** 这就是其 O(N+M) 线性时间复杂度的奥秘。

整个算法可以分解为两个主要阶段：

### **核心算法思路 (C++ 和 C 共享)**

1.  **预处理模式串（构建 `next` / LPS 数组）**：
    *   **目标**：构建一个 `next` 数组（有时也称为 `lps` (Longest Proper Prefix Suffix) 数组），其中 `next[i]` (或 `next[i-1]`) 存储了模式串 `needle` 中以 `needle[i-1]` 结尾的子串（长度为 `i`）的**最长公共前缀后缀的长度**。
    *   **`next[j]` 的含义**：如果 `needle[0...j-1]` 是模式串的一个前缀，那么 `next[j-1]` 表示这个前缀的最长真前缀，它同时也是这个前缀的最长真后缀。
    *   **实现**：
        *   `next` 数组的大小与模式串 `needle` 的长度 `M` 相同。
        *   `next[0]` 总是 `0` (单个字符没有公共前缀后缀)。
        *   使用两个指针 `i` (遍历后缀) 和 `j` (遍历前缀)。
        *   当 `needle[i]` 与 `needle[j]` 匹配时，`j` 和 `i` 都递增，并设置 `next[i] = j`。
        *   当 `needle[i]` 与 `needle[j]` 不匹配时：
            *   如果 `j > 0`，说明模式串有已匹配的前缀部分，需要根据 `next[j-1]` 指导，将 `j` 回溯到上一个已知的最长公共前缀后缀的长度。
            *   如果 `j == 0`，说明模式串的前缀没有匹配，`next[i]` 保持为 `0`，`i` 递增。

2.  **主串匹配**：
    *   **目标**：利用预处理好的 `next` 数组，在主串 `haystack` 中高效地查找模式串 `needle`。
    *   **实现**：
        *   同样使用两个指针 `i` (遍历主串 `haystack`) 和 `j` (遍历模式串 `needle`)。
        *   **当 `haystack[i]` 与 `needle[j]` 匹配时**：`i` 和 `j` 都递增。
        *   **当 `haystack[i]` 与 `needle[j]` 不匹配时**：
            *   如果 `j > 0` (模式串有已匹配部分)，则将 `j` 移动到 `next[j-1]` 的位置。这意味着模式串向右滑动了 `j - next[j-1]` 位，并从新的 `j` 位置继续比较，避免了从头开始的主串指针回溯。
            *   如果 `j == 0` (模式串的第一个字符就没匹配上)，则只将主串指针 `i` 递增，模式串指针 `j` 保持 `0`。
        *   **匹配成功**：当 `j` 的值达到 `needle` 的长度 `M` 时，表示在 `haystack` 中找到了一个匹配。此时，匹配的起始索引是 `i - M + 1`。找到第一个匹配后即可返回。

### **边界条件处理**

*   **空模式串 (`needle` 为空)**：始终返回 `0`。这是约定俗成的。
*   **空主串 (`haystack` 为空)**：如果 `needle` 非空，则返回 `-1`。
*   **主串短于模式串**：返回 `-1`。

### **C++ 版本和 C 版本在实现细节上的核心差异**

核心算法是完全一致的，因为 KMP 是一种算法逻辑。差异主要在于语言特有的数据结构和内存管理：

#### **C++ 版本解决问题的核心**

*   **`std::string` 和 `std::vector<int>`**：C++ 版本利用这些标准库容器，自动处理字符串的长度、访问和 `next` 数组的内存管理。这简化了代码，减少了手动内存分配和释放的错误。
*   **类封装**：`class Solution { public: ... };` 结构是 C++ 解决 LeetCode 问题的典型范式。
*   **Linus 哲学**：在 C++ 中，优先使用健壮的标准库特性来处理字符串和数组，避免手写内存管理。这样代码更简洁，更不容易出错，符合“实用主义”和“好品味”。

#### **C 版本解决问题的核心**

*   **`char*` 和 `strlen()`**：C 版本直接操作 C 风格字符串 `char*`，并使用 `strlen()` 来获取长度。
*   **手动内存管理**：`next` 数组需要通过 `malloc` 或 `calloc` 显式分配内存，并在函数结束前通过 `free` 显式释放。这是 C 语言的核心责任。
*   **空指针检查**：对于 `char*` 参数，需要显式检查 `NULL` 指针，以防止解引用空指针导致崩溃。
*   **类型转换**：`size_t` 和 `int` 之间的比较和转换需要小心，可能需要强制类型转换以避免编译警告。
*   **Linus 哲学**：在 C 中，所有内存都必须**显式管理**。KMP 算法虽然复杂，但其在 C 语言中的实现，展示了对内存和指针的极致控制，以及在没有高级抽象的情况下，通过精确的逻辑实现高效算法的能力。每个 `malloc` 必须有对应的 `free`。

**总而言之，KMP 算法的核心解决思路在于通过预先计算模式串的 `next` 数组，在主串匹配失败时，能够高效地“跳过”不必要的比较，从而实现线性的时间复杂度。这是利用模式串自身结构来优化匹配过程的典范。**

### Way Of C

> source code

```c
int strStr(char* haystack, char* needle) {
    if (needle == NULL || *needle == '\0') return 0;

    if (haystack == NULL) return -1;

    size_t h_len = strlen(haystack);
    size_t n_len = strlen(needle);

    if (h_len <n_len) return -1;

    int *next = (int *)calloc(n_len, sizeof(int));
    if (next == NULL) {
        fprintf(stderr, "strStr(KMP).\n");
        exit(EXIT_FAILURE);
    }

    for (int i = 1, j = 0; i < n_len; ++i) {
        while (j > 0 && needle[i] != needle[j]) j = next[j - 1];
        if (needle[i] == needle[j]) ++j;
        next[i] = j;
    }

    int result_idx = -1;

    for (int i = 0, j = 0; i < h_len; ++i) {
        while (j > 0 && haystack[i] != needle[j]) j = next[j - 1];
        if (haystack[i] == needle[j]) ++j;
        if (j == (int)n_len) {
            result_idx = i - n_len + 1;
            break;
        }
    }

    free(next);

    return result_idx;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle.empty()) return 0;
        int n_len = needle.length();
        int h_len = haystack.length();

        if (h_len < n_len) return -1;

        vector<int> next(n_len, 0);
        for (int i = 1, j = 0; i < n_len; ++i) {
            while (j > 0 && needle[i] != needle[j]) j = next[j - 1];
            if (needle[i] == needle[j]) ++j;
            next[i] = j;
        }

        for (int i = 0, j = 0; i < h_len; ++i) {
            while (j > 0 && haystack[i] != needle[j]) j = next[j - 1];
            if (haystack[i] == needle[j]) ++j;
            if (j == n_len) return i - n_len + 1;
        }

        return -1;
    }
};
```

# Question

[longest-palindromic-substring](https://leetcode.cn/problems/longest-palindromic-substring/)



# Answer

> solution

### **核心解决思路：中心扩展法 (Expand Around Center)**

**这是解决“最长回文子串”问题最直接、最常用且效率不错的算法之一。其核心思想是：**

1.  **回文中心：** 任何回文子串都有一个“中心”。这个中心可以是：
    *   **单个字符：** 对于奇数长度的回文串（如 "aba"），中心就是中间的 'b'。
    *   **两个字符之间的空隙：** 对于偶数长度的回文串（如 "abba"），中心就是两个 'b' 之间的空隙。
2.  **遍历所有可能的中心：**
    *   对于字符串 `S` 中的每个字符 `S[i]`，我们都假设它是一个奇数长度回文串的中心。
    *   对于字符串 `S` 中每个字符 `S[i]` 和 `S[i+1]` 之间的空隙，我们都假设它是一个偶数长度回文串的中心。
3.  **向外扩展：**
    *   从每个可能的中心开始，同时向左和向右扩展。
    *   在扩展过程中，只要左右两边的字符相等且未超出字符串边界，就继续扩展。
    *   一旦遇到不相等的字符或到达边界，扩展就停止。此时，你已经找到了以该中心为起点的最长回文子串。
4.  **更新最长结果：**
    *   在每次扩展后，比较当前找到的回文子串的长度与之前记录的最长回文子串的长度。
    *   如果当前找到的更长，就更新全局的最长回文子串的起始位置和长度。

**算法复杂度：**
*   **时间复杂度：** O(N^2)。字符串有 N 个字符，所以有大约 2N-1 个可能的中心。每个中心最坏情况下需要扩展 O(N) 次。
*   **空间复杂度：** O(1) (如果我们只存储最长回文的起始和长度，不复制整个子串)。

### Way Of C

> think

**字符串表示与操作：**
    *   **C：`char*`**
        *   **表示：** 以 `\0` 结尾的字符数组 (`char*` 指针)。
        *   **长度：** `strlen(s)` (O(N) 时间，每次调用都需要遍历字符串)。
        *   **字符访问：** `s[i]` (无边界检查，需要手动确保不越界)。
        *   **子串截取：** 无内置函数。需要：
            1.  手动 `malloc` 一块新的内存。
            2.  使用 `strncpy` 将子串内容复制到新内存。
            3.  手动在复制内容的末尾添加 `\0` (因为 `strncpy` 不保证空终止)。
        *   **内存：** 必须手动使用 `malloc`、`realloc`、`free` 来分配和释放内存。

> source code

```c
void expandAroundCenter(const char* s, int s_len, int left_init, int right_init,
                        int* out_start_idx, int* out_len) {
    int left = left_init;
    int right = right_init;

    while (left >= 0 && right < s_len && s[left] == s[right]) {
        --left;
        ++right;
    }
    int current_len = right - left - 1;
    if (current_len > *out_len) {
        *out_start_idx = left + 1;
        *out_len = current_len;
    }
}

char* longestPalindrome(char* s) {
    if (s == NULL) {
        return NULL;
    }

    int s_len = strlen(s);
    if (s_len == 0) {
        return "";
    }

    int start_idx = 0;
    int max_len = 0;

    if (s_len == 1) {
        return s;
    }
    
    max_len = 1;

    for (int i = 0; i < s_len; ++i) {
        expandAroundCenter(s, s_len, i, i, &start_idx, &max_len);

        if (i + 1 < s_len) {
            expandAroundCenter(s, s_len, i, i + 1, &start_idx, &max_len);
        }
    }

    char* result = (char*)malloc(max_len + 1);
    if (result == NULL) {
        fprintf(stderr, "Error: malloc failed for palindrome result.\n");
        char* empty_str = (char*)malloc(1);
        if (empty_str) {
            empty_str[0] = '\0';
        } else {
            exit(EXIT_FAILURE);
        }
        return empty_str;
    }
    
    strncpy(result, s + start_idx, max_len);
    result[max_len] = '\0';

    return result;
}
```

---


### Way Of C++

> think

**字符串表示与操作：**
    *   **C++：`std::string`**
        *   **表示：** 抽象的 `std::string` 对象。
        *   **长度：** `s.size()` (O(1) 时间)。
        *   **字符访问：** `s[i]` (边界检查可选，通常不检查)。
        *   **子串截取：** `s.substr(start, length)`，直接返回一个新的 `std::string` 对象，自动管理内存。
        *   **内存：** 自动管理，无需手动 `new`/`delete` 或 `malloc`/`free`。

> source code

```c++
class Solution {
public:
    std::pair<int, int> expandAroundCenter(const std::string &s, int left, int right) {
        while (left >= 0 && right < s.size() && s[left] == s[right]) --left, ++right;
        return {left + 1, right - left - 1};
    }

    string longestPalindrome(string s) {
        if (s.empty()) return "";
        int start_idx = 0, max_len = 0;
        for (int i = 0; i < s.size(); ++i) {
            std::pair<int, int> p1 = expandAroundCenter(s, i, i);
            if (p1.second > max_len) {
                start_idx = p1.first;
                max_len = p1.second;
            }

            if (i + 1 < s.size()) {
                std::pair<int, int> p2 = expandAroundCenter(s, i, i + 1);
                if (p2.second > max_len) {
                    start_idx = p2.first;
                    max_len = p2.second;
                }
            }
        }
        return s.substr(start_idx, max_len);
    }
};
```

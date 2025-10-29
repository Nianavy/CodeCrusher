# Question

[valid-number](https://leetcode.cn/problems/valid-number/)



# Answer

> solution

### **解决思路总结：严格的分段指针解析法**

**格言：** “一个复杂的问题，应该分解为一系列简单、不可逆的步骤。如果一个步骤失败了，整个过程就失败了。”

**核心思想：**
一个有效的数字字符串，无论多么复杂（包含符号、小数点、科学计数法），都必须严格遵循一个固定的结构顺序。我们采用线性扫描，将字符串分解为七个必须按顺序处理的阶段。通过指针在前进行走，我们确保在任何阶段，如果遇到预期之外的字符，或者未满足该阶段的最小要求，则立即判定失败。

**七个必须按顺序处理的阶段：**

1.  **跳过前导空格 (Leading Spaces)：** 忽略开头的任意空格。
2.  **符号位 (Sign)：** 检查可选的 `+` 或 `-` 符号，最多只能有一个，并前进指针。
3.  **整数部分 (Integer)：** 查找并跳过任意数量的数字。
4.  **小数点 (Dot)：** 如果发现 `.`，则进入小数部分。
    *   **约束检查：** 此时，数字主体（整数部分或小数部分）必须至少包含一个数字（即 `.` 前后至少有一个数字）。如果小数点前后都没有数字，则无效（如 `.` 或 `+ .`）。
5.  **指数符号 (Exponent 'e'/'E')：** 如果发现 `e` 或 `E`，则进入科学计数法部分。
    *   **约束检查：** `e` 之前必须已经识别到数字主体（即在第 3/4 阶段必须找到过数字）。
6.  **指数符号 (Exponent Sign)：** 检查指数部分的符号 (`+` 或 `-`)，是可选的，最多只能有一个，并前进指针。
7.  **指数数字 (Exponent Digits)：** 查找并跳过指数部分的数字。
    *   **约束检查：** `e` 后面（紧跟符号后）必须至少有一个数字。如果 `e` 后没有数字，则无效（如 `1e` 或 `1e+`）。

8.  **跳过尾随空格 (Trailing Spaces)：** 忽略尾部的任意空格。

**最终验证：**
在所有阶段处理完毕后，执行最终的、不可妥协的检查：
*   **指针位置：** 遍历指针必须精确地停在字符串的末尾（空字符 `\0` 或 `s.size()`）。
*   **数字主体：** 在第 3/4 阶段中，必须至少发现过一个数字（即 `num_found` 必须为 `true`）。

**实用主义的体现：**
这种分段解析法避免了复杂的正则表达和昂贵的状态机实现，同时确保了 O(N) 的性能和逻辑的健壮性。每个阶段的逻辑是隔离且明确的，易于维护和调试，是解决这种复杂解析问题的最“实用”方法。

### Way Of C

> source code

```c
// 辅助函数：跳过前导空格
static void skip_leading_spaces(const char **s) {
    while (**s == ' ') {
        (*s)++;
    }
}

// 辅助函数：跳过数字
static bool skip_digits(const char **s) {
    bool found = false;
    while (isdigit(**s)) {
        found = true;
        (*s)++;
    }
    return found;
}

bool isNumber(char* s) {
    const char *ptr = s;
    bool num_found = false; // 是否在数字主体部分找到数字
    
    // 1. 跳过前导空格
    skip_leading_spaces(&ptr);

    // 2. 处理初始符号
    if (*ptr == '+' || *ptr == '-') {
        ptr++;
    }

    // 3. 处理整数部分
    num_found = skip_digits(&ptr);

    // 4. 处理小数部分
    if (*ptr == '.') {
        ptr++;
        // 小数点后是否有数字？如果小数点前没有数字，小数点后必须有
        num_found = skip_digits(&ptr) || num_found;
    }
    
    // 如果数字主体没有找到任何数字 (例如: "+", ".", "+.")
    if (!num_found) {
        return false;
    }

    // 5. 处理指数部分 ('e' 或 'E')
    if (*ptr == 'e' || *ptr == 'E') {
        ptr++;
        bool exp_num_found = false;

        // 检查指数符号
        if (*ptr == '+' || *ptr == '-') {
            ptr++;
        }
        
        // 检查指数数字部分
        exp_num_found = skip_digits(&ptr);

        // 指数符号后必须跟数字 (e.g., "e5" 有效, "e+" 无效)
        if (!exp_num_found) {
            return false;
        }
    }

    // 6. 跳过尾随空格
    skip_leading_spaces(&ptr);

    // 7. 最终检查：指针是否到达字符串末尾 ('\0')
    return *ptr == '\0';
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    // 辅助函数：跳过前导空格
    void skip_leading_spaces(const std::string& s, int& i) {
        while (i < s.size() && s[i] == ' ') {
            i++;
        }
    }

    // 辅助函数：跳过数字
    bool skip_digits(const std::string& s, int& i) {
        bool found = false;
        while (i < s.size() && isdigit(s[i])) {
            found = true;
            i++;
        }
        return found;
    }

    bool isNumber(std::string s) {
        int i = 0;
        bool num_found = false; // 是否在当前数字段找到数字
        
        // 1. 跳过前导空格
        skip_leading_spaces(s, i);

        // 2. 处理初始符号
        if (i < s.size() && (s[i] == '+' || s[i] == '-')) {
            i++;
        }

        // 3. 处理整数部分
        num_found = skip_digits(s, i);

        // 4. 处理小数部分
        if (i < s.size() && s[i] == '.') {
            i++;
            // 小数点后必须有数字 (e.g., "1.2") 或小数点前有数字 (e.g., "1.")
            // 只要小数点前后有一个有数字，就是有效的 (e.g., ".1" 或 "1.")
            num_found = skip_digits(s, i) || num_found;
        }
        
        // 如果数字主体（整数/小数）没有找到任何数字，则无效 (e.g., "+", "-", ".", "+.")
        if (!num_found) {
            return false;
        }

        // 5. 处理指数部分 ('e' 或 'E')
        if (i < s.size() && (s[i] == 'e' || s[i] == 'E')) {
            i++;
            bool exp_num_found = false;

            // 检查指数符号
            if (i < s.size() && (s[i] == '+' || s[i] == '-')) {
                i++;
            }
            
            // 检查指数数字部分
            exp_num_found = skip_digits(s, i);

            // 指数符号后必须跟数字 (e.g., "e5" 有效, "e+" 无效)
            if (!exp_num_found) {
                return false;
            }
        }

        // 6. 跳过尾随空格
        skip_leading_spaces(s, i);

        // 7. 最终检查：指针是否到达字符串末尾 (i == s.size())
        return i == s.size();
    }
};
```

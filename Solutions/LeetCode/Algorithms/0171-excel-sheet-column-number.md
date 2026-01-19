# Question

[excel-sheet-column-number](https://leetcode.cn/problems/excel-sheet-column-number/)



# Answer

> solution

**核心思路：将问题视为标准的 26 进制（1-based）转换，用单循环计算。**

1.  **数据流**：从左到右遍历字符串。
2.  **状态维护**：一个结果变量 `result`。
3.  **转换规则**：`result = result * 26 + (c - 'A' + 1)`。每个字符的值是 `c - 'A' + 1`，权重为 26^（从右侧数的位置）。
4.  **为什么简单而正确**：避免了条件分支和多变量状态跟踪，直接映射数学定义。

### Way Of C

> source code

```c
int titleToNumber(char* columnTitle) {
    long long result = 0; // 使用 long long 以防止中间计算溢出（对于非常长的列名），但题目通常保证在 int 范围内。
    
    while (*columnTitle != '\0') {
        // 逻辑与 C++ 版本完全一致。
        result = result * 26 + (*columnTitle - 'A' + 1);
        columnTitle++;
    }
    
    // 根据题目要求返回 int，确保不溢出。
    // 在 LeetCode 的约束下，结果在 int 范围内。
    return (int)result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int titleToNumber(string columnTitle) {
        int result = 0;
        for (char c : columnTitle) {
            // 直接计算：当前结果左移一位（乘26），加上当前字符的数值。
            // 'A' = 1, 'B' = 2, ..., 'Z' = 26。所以是 c - 'A' + 1
            result = result * 26 + (c - 'A' + 1);
        }
        return result;
    }
};
```

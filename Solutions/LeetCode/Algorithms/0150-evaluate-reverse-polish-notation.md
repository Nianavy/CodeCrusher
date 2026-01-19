# Question

[evaluate-reverse-polish-notation](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)



# Answer

> solution

**RPN 求值器设计思路总结**

1.  **数据结构选择**：**必须使用动态栈**。固定大小的数组（如 `stack[500]`）是灾难，会导致缓冲区溢出。在 C 中，手动管理动态内存；在 C++ 中，直接使用 `std::stack`。

2.  **核心逻辑**：单次遍历。遇到**数字**就**压栈**；遇到**操作符**就弹出两个数**计算**，结果**压栈**。这是问题的本质，任何复杂实现都是错误。

3.  **数值解析**：这是最关键的部分。**绝对不要自己写 `a2i`**。
    *   **C 语言**：使用标准库函数 `strtol` 或 `strtoll`。它能正确、安全地处理所有情况：正数、负数（`"-5"`）、带 `+` 号的数（`"+5"`）、大整数。这是最简单、最健壮的方法。
    *   **C++ 语言**：优先用 `std::strtol`，避免 `stoi` 的异常。或者直接用 `std::stringstream`。但 `strtol` 是 C++ 和 C 通用的，且更底层、更快。

4.  **操作符判断**：解析完数字后，如果解析结束指针 `endptr` 指向字符串结尾，说明是数字，压栈。否则，是操作符，执行计算。

5.  **除法与溢出**：LeetCode 题目保证除法整除且分母非零。但为了健壮性，评估潜在整数溢出（使用 `long long` 更安全），并明确除法是**整数除法**（C/C++ 中的 `/` 对整数即为向零取整）。

6.  **错误处理**：在生产环境中，必须检查栈大小（`len < 2` 时弹出操作数会崩溃）和输入有效性。但在 LeetCode 这样的受控环境中，可以假设输入完全合法。

### Way Of C

> source code

```c
int evalRPN(char** tokens, int tokensSize) {
    // 使用长整型的栈以避免溢出。这里使用动态数组演示。
    // 在实际系统中，如果表达式已知小，固定数组可以，但这是个坏习惯。
    // 为了通用性，我们使用堆上动态数组。
    if (tokensSize == 0) return 0;
    long long* stack = malloc(tokensSize * sizeof(long long));
    if (!stack) return 0; // 内存分配失败
    int len = 0;
    for (int i = 0; i < tokensSize; i++) {
        char* endptr;
        // 使用 strtoll 解析整数，它能正确处理前导空格、'+'、'-'、"0x" 等。
        long long a = strtoll(tokens[i], &endptr, 10);
        // 检查 `endptr` 是否指向字符串结尾。是的话，则这是一个数字。
        if (*endptr == '\0') {
            stack[len++] = a;
        } else {
            // 不是数字，根据第一个字符判断是哪个操作符。
            // 题目保证只有这四个操作符。
            if (len < 2) {
                // 错误：栈中不足两个操作数
                free(stack);
                return 0;
            }
            long long b = stack[--len];
            long long b_op = stack[--len]; // 实际上 a 已覆盖
            long long result;
            switch (tokens[i][0]) {
                case '+': result = b_op + b; break;
                case '*': result = b_op * b; break;
                case '/': result = b_op / b; break; // 注意：题目保证 b != 0
                case '-': result = b_op - b; break;
                default: // 理论上不应该发生，但为了安全
                    free(stack);
                    return 0;
            }
            stack[len++] = result;
        }
    }
    // 根据题目保证，最终栈中应恰好有一个元素
    long long result = (len > 0) ? stack[len - 1] : 0;
    free(stack);
    return (int)result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int evalRPN(std::vector<std::string>& tokens) {
        std::stack<long long> stk; // 使用 long long 以避免溢出问题
        for (const std::string& s : tokens) {
            char* endptr;
            long long val = std::strtoll(s.c_str(), &endptr, 10);
            // strtoll 完美处理：+5, -5, 0, 2147483648 等。
            if (*endptr == '\0') {
                // 这是一个数字
                stk.push(val);
            } else {
                // 这是一个操作符
                long long b = stk.top(); stk.pop();
                long long a = stk.top(); stk.pop();
                if (s == "+") stk.push(a + b);
                else if (s == "-") stk.push(a - b);
                else if (s == "*") stk.push(a * b);
                else if (s == "/") stk.push(a / b); // 注意：题目保证除法为整数除法，且不为零
            }
        }
        return static_cast<int>(stk.top());
    }
};
```

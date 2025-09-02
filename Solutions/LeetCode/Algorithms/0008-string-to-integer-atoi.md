# Question

[string-to-integer-atoi](https://leetcode.cn/problems/string-to-integer-atoi/)



# Answer

> solution

**`atoi` 实现的四大核心步骤和挑战：**

### **1. 清理和初始化 (Whitespace & Sign Handling)**

*   **跳过前导空格：** 遍历字符串，忽略所有连续的空格字符。这是第一步，也是必须的。
    *   **挑战：** 如果字符串全是空格，或者跳过空格后字符串就结束了，应该直接返回 `0`。
*   **确定符号：** 跳过空格后，检查第一个非空格字符。
    *   如果是 `'-'`，记录符号为负数，并跳过该字符。
    *   如果是 `'+'`，记录符号为正数（或保持默认），并跳过该字符。
    *   **挑战：** 符号只能有一个。如果第一个非空格字符不是 `'-'` 或 `'+'`，或者在符号之后紧跟着的不是数字，则视为无符号或无效格式，数字解析在此停止。
*   **无效字符处理：** 如果跳过空格和可选符号后，第一个字符不是数字，则视为无效输入，返回 `0`。

### **2. 数字累加 (Digit Accumulation)**

*   **遍历数字：** 继续遍历字符串，只要遇到数字字符 (`'0'` 到 `'9'`)，就将其转换为对应的整数值，并累加到结果中。
    *   **转换公式：** `result = result * 10 + digit;` (这是最简单的累加方式)
    *   **挑战：** 如果遇到非数字字符，停止数字累加。

### **3. 溢出检查 (Crucial Overflow Detection)**

这是 `atoi` 的**核心挑战**，也是最容易出错的地方。必须在**每次数字累加之前**进行检查，以防止中间结果溢出 `int` 范围，从而导致检查条件失效或未定义行为 (Undefined Behavior, UB)。

*   **策略选择：**
    *   **方法 A (使用 `long long` 中间结果)：** 这是最简单的。用 `long long` 类型的变量 `result_long` 来累加数字，然后在循环结束后，检查 `result_long` 是否超出了 `INT_MAX` 或 `INT_MIN`。如果超出，返回 `INT_MAX` 或 `INT_MIN`。
        *   **优点：** 检查逻辑非常直观，不易出错。
        *   **缺点：** 依赖 `long long` 类型，可能不符合“纯 `int` 实现”的要求。
    *   **方法 B (纯 `int` 累加，统一负数策略)：** 这是我的代码中用的，也是最健壮、最优雅的纯 `int` 方案。
        *   **核心思想：** 始终将 `result` 变量作为**负数**进行累加（即使最终结果是正数）。
            *   为什么是负数？因为 `INT_MIN` 的绝对值比 `INT_MAX` 大 1 (`-2^31` vs `2^31-1`)，这使得负数范围的溢出检测更具对称性，更容易处理 `INT_MIN` 的特殊情况。
        *   **溢出检查点：** 在 `result = result * 10 - digit;` 之前，检查 `result` 是否会因这次操作而低于 `INT_MIN`。
            *   检查条件：`result < INT_MIN / 10`
            *   边界条件：`result == INT_MIN / 10 && digit >= -(INT_MIN % 10)`
                *   `INT_MIN / 10` 是 `int` 能达到的最小负数除以 10 的结果。
                *   `-(INT_MIN % 10)` 是 `INT_MIN` 的最后一位数字的绝对值（例如，`-2147483648` 的 `-(%10)` 是 `8`）。
                *   如果 `result` 达到 `INT_MIN / 10` 且 `digit` 大于或等于 `-(INT_MIN % 10)`，就意味着会溢出 `INT_MIN`。
        *   **返回处理：** 一旦检测到溢出，立即根据之前记录的 `sign` 返回 `INT_MAX` 或 `INT_MIN`。例如，如果 `sign` 是 `1` 但累加超出了负数范围，就意味着正数溢出，应该返回 `INT_MAX`。
    *   **挑战：`INT_MIN` 的取反是 UB！** `INT_MIN` 在补码表示下没有对应的正数表示。任何 `-INT_MIN` 的操作都会触发未定义行为。因此，在最终返回结果时，如果 `sign == 1` 且 `result` 恰好是 `INT_MIN`，你不能直接 `return -result;`。我的纯 `int` 方案通过在溢出检查时就捕获这种情况并返回 `INT_MAX`，从而避免了这个问题。

### **4. 最终结果 (Finalization)**

*   **应用符号：**
    *   如果累加时使用了统一负数策略，并且最终的 `sign` 是 `1` (正数)，则将累加的负数结果取反（`-result`）返回。
    *   如果 `sign` 是 `-1` (负数)，则直接返回累加的负数结果。

**总结一下：**

一个健壮的 `atoi` 实现，最重要的是避免未定义行为，并在所有边界情况下给出正确的、可预测的结果。这要求你在数字累加的每一步都保持警惕，**提前** 检查潜在的溢出，并充分理解 `int` 类型的限制，尤其是 `INT_MIN` 的不对称性。


### Way Of C

> source code

```c
int myAtoi(char* s) {
    int k = 0;
    while (s[k] != '\0' && s[k] == ' ') ++k;
    if (s[k] == '\0') return 0;
    int sign = 1;
    if (s[k] == '-') {
        sign = -1;
        ++k;
    }
    else if (s[k] == '+') ++k;

    int result = 0;
    while (s[k] != '\0' && s[k] >= '0' && s[k] <= '9') {
        int digit = s[k] - '0';
        if (result < INT_MIN / 10 || (result == INT_MIN / 10 && digit >= -(INT_MIN % 10))) {
            return (sign == 1) ? INT_MAX : INT_MIN;
        }
        result = result * 10 -digit;
        ++k;
    }
    if (sign == 1) return -result;
    else return result;
}
```

---


### Way Of C++

> source code

```c++
class Solution {
public:
    int myAtoi(std::string s) {
        int k = 0;
        while (k < s.size() && s[k] == ' ') ++k;

        if (k == s.size()) return 0;

        int sign = 1;
        if (s[k] == '-') {
            sign = -1;
            ++k;
        } 
        else if (s[k] == '+') ++k;

        int result = 0;

        while (k < s.size() && s[k] >= '0' && s[k] <= '9') {
            int digit = s[k] - '0';

            if (result < INT_MIN / 10 || (result == INT_MIN / 10 && digit >= -(INT_MIN % 10))) {
                return (sign == 1) ? INT_MAX : INT_MIN;
            }
            
            result = result * 10 - digit;
            ++k;
        }

        if (sign == 1) return -result;
        else return result;
    }
};
```

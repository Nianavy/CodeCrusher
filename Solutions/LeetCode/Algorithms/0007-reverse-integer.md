# Question

[reverse-integer](https://leetcode.cn/problems/reverse-integer/)



# Answer

> solution

### **1. 基本反转逻辑 (核心数学操作)**

这部分很简单，就是通过数学运算，将一个整数的数字“拆分”出来，然后再“重组”成反过来的顺序。

1.  **逐位获取数字：**
    *   利用**取模运算 (`% 10`)** 来获取当前数字的**最低位（个位数）**。
        *   例如：`123 % 10 = 3`
    *   利用**除法运算 (`/ 10`)** 来“移除”当前数字的最低位，使其变为下一个要处理的数字。
        *   例如：`123 / 10 = 12`

2.  **构建反转后的数字：**
    *   你需要一个变量（例如 `reversed_num`）来存储构建中的反转数字。
    *   在每次循环中，将 `reversed_num` **乘以 10**（相当于将其当前数字向左移动一位），然后**加上**刚刚获取的最低位数字。
        *   例如：
            *   初始 `reversed_num = 0`
            *   获取 `3`：`reversed_num = 0 * 10 + 3 = 3`
            *   获取 `2`：`reversed_num = 3 * 10 + 2 = 32`
            *   获取 `1`：`reversed_num = 32 * 10 + 1 = 321`

3.  **循环终止条件：**
    *   当原始数字 `x` 通过不断除以 `10` 最终变为 `0` 时，循环结束。

**这部分逻辑对于正数和负数都适用** (例如 `-123 % 10` 结果是 `-3`，`-123 / 10` 结果是 `-12`，所以逻辑是自洽的)。

---

### **2. 溢出检测与处理 (真正的难点和关键)**

这是这个问题的**核心所在**。因为 `int` 类型有其固定的最大值 (`INT_MAX`) 和最小值 (`INT_MIN`)。反转后的数字很可能超出这个范围。

**最健壮、最简洁的解决思路是：使用一个比 `int` 范围更大的类型作为中间存储。**

1.  **使用 `long long` 中间变量：**
    *   声明一个 `long long` 类型的变量（例如 `reversed_num`）来存储正在构建中的反转数字。
    *   `long long` 通常是 64 位，比 32 位的 `int` 拥有大得多的范围。这意味着即使反转后的数字溢出了 `int` 的范围，它仍然可以在 `long long` 中被安全地存储和比较。

2.  **在每一步构建后进行检测：**
    *   在每次 `reversed_num = reversed_num * 10 + digit;` 操作之后，立即检查 `reversed_num` 的值。
    *   将其与 `INT_MAX` (C 标准库 `limits.h` 中定义的 `int` 类型最大值) 和 `INT_MIN` (C 标准库 `limits.h` 中定义的 `int` 类型最小值) 进行比较。
    *   `if (reversed_num > INT_MAX || reversed_num < INT_MIN)`

3.  **处理溢出：**
    *   如果检测到 `reversed_num` 已经超出了 `int` 的范围，立即停止循环，并根据题目要求返回特定的值（通常是 `0`）。

4.  **安全返回：**
    *   如果循环顺利完成，没有触发溢出检测，那么最终 `reversed_num` 的值就保证在 `int` 范围内。
    *   此时，可以安全地将 `long long` 类型的 `reversed_num` 强制转换回 `int` 类型并返回。

**为什么这种方法是“好品味”？**

*   **直观：** 逻辑非常直接，“如果超过了最大值就报错”。
*   **安全：** 利用了类型系统提供的范围安全性，避免了复杂的数学预判和可能产生的边缘错误。
*   **简洁：** 溢出检测的代码只有一行，易于理解和维护。

**总而言之，整数反转的核心思路就是：通过循环不断地“剥离”数字的末位并“添加”到另一个数字的末位，同时，使用一个更大范围的中间类型来安全地捕捉并处理溢出情况。**

### Way Of C

> source code

```c
int reverse(int x) {
    if (!x) return 0;
    long long reversed_num = 0;
    while (x) {
        int digit = x % 10;
        reversed_num = reversed_num * 10 + digit;
        if (reversed_num > INT_MAX || reversed_num < INT_MIN) return 0;
        x /= 10;
    }
    return (int)reversed_num;
}
```

---


### Way Of C++

> source code

```c++
class Solution {
public:
    int reverse(int x) {
        if (!x) return 0;
        long long r = 0;
        while (x) {
            r = r * 10 + x % 10;
            if (r > INT_MAX || r < INT_MIN) return 0;
            x /= 10;
        }
        return r;
    }
};
```

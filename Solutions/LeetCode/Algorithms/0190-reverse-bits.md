# Question

[reverse-bits](https://leetcode.cn/problems/reverse-bits/)



# Answer

> solution

**核心思想**：用无符号整数进行位操作，避免有符号左移导致的未定义行为。

**步骤**：
1.  接受 `int` 输入，将其视为比特模式。
2.  转换为 `unsigned` 类型，安全地执行分治法交换相邻比特位。
3.  反转完成后，转换回 `int` 输出，保持比特模式不变。

**优点**：常数时间，无循环，无 UB，代码直接。

### Way Of C

> source code

```c
int reverseBits(int n) {
    // 将输入视为比特模式，使用 unsigned int 进行安全的位运算
    unsigned u = (unsigned)n;
    // 交换相邻的 1 位比特
    u = ((u & 0x55555555) << 1) | ((u >> 1) & 0x55555555);
    // 交换相邻的 2 位比特
    u = ((u & 0x33333333) << 2) | ((u >> 2) & 0x33333333);
    // 交换相邻的 4 位比特
    u = ((u & 0x0f0f0f0f) << 4) | ((u >> 4) & 0x0f0f0f0f);
    // 交换相邻的 8 位比特
    u = ((u & 0x00ff00ff) << 8) | ((u >> 8) & 0x00ff00ff);
    // 交换相邻的 16 位比特（实际上就是整个交换）
    // 注意：这里对 unsigned int 进行左移是安全的，因为 u 的高位为 0
    u = (u << 16) | (u >> 16);
    // 最后，我们需要确保 u 的高位 16 位与 n 的符号位模式相匹配吗？
    // 实际上不需要。因为我们是在操作比特模式。
    // 但 n 是 int，返回的也必须是 int，它代表的是比特模式的反转。
    // 所以直接返回这个反转后的比特模式即可。
    return (int)u;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int reverseBits(int n) {
        // 同样，使用 unsigned int 进行安全的位操作
        unsigned u = (unsigned)n;
        u = ((u & 0x55555555) << 1) | ((u >> 1) & 0x55555555);
        u = ((u & 0x33333333) << 2) | ((u >> 2) & 0x33333333);
        u = ((u & 0x0f0f0f0f) << 4) | ((u >> 4) & 0x0f0f0f0f);
        u = ((u & 0x00ff00ff) << 8) | ((u >> 8) & 0x00ff00ff);
        u = (u << 16) | (u >> 16);
        return (int)u;
    }
};
```

# Question

[excel-sheet-column-title](https://leetcode.cn/problems/excel-sheet-column-title/)



# Answer

> solution

**解决思路**

1.  **问题本质**：给定整数 `n`（1 ≤ n ≤ 2^31-1），将其转换为 Excel 列名（A=1, B=2, ..., Z=26, AA=27, ...）。
2.  **核心转换**：这本质上是一个 **26 进制转换问题**，但进制转换的标准是从 0 开始（0=0, 1=1, ...）。但由于 Excel 列名是 1-based 索引，我们需要将其映射为 0-based：
    *   `n - 1`：将 1 映射为 0，26 映射为 25。
    *   `(n - 1) % 26`：得到当前最低位的数字（0-25），对应字符 `A` 到 `Z`。
    *   `(n - 1) / 26`：向更高位迭代。
3.  **算法步骤**：
    *   **初始化**：一个空字符串用于构建结果。
    *   **循环**：使用 `do-while` 循环（确保至少处理 `n=1` 的情况）。
        *   先 `n--`（0-based 映射）。
        *   将当前数字转换为字符并添加到字符串：`'A' + (n % 26)`。
        *   更新 `n`：`n /= 26`。
    *   **结束条件**：当 `n` 变为 0 时停止。
    *   **调整顺序**：构建的字符串是反向的，需要 `reverse` 得到正确的顺序。
4.  **关键技巧**：使用 `do-while` 循环，避免在循环体外部单独处理 `n=0` 这种特殊情况。让循环逻辑统一。

**一句话总结**：**递归地执行 `n--`、取模、存储、除法，直到 `n` 为 0，然后反转结果。** 这就是最直接、最清晰的解法，没有多余的循环和条件判断。

### Way Of C

> source code

```c
char* convertToTitle(int n) {
    // 分配一个足够大的缓冲区。对于一个32位整数，最大表示为 26^6 左右，所以 8 个字符绰绰有余。1024 是浪费。
    char *result = malloc(16);
    if (!result) return ""; // 简单的错误处理，避免 CRASH
    int len = 0;

    do {
        n--;
        result[len++] = 'A' + (n % 26);
        n /= 26;
    } while (n > 0);

    result[len] = '\0';

    // 原地反转
    char temp;
    for (int i = 0, j = len - 1; i < j; i++, j--) {
        temp = result[i];
        result[i] = result[j];
        result[j] = temp;
    }

    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    string convertToTitle(int n) {
        string result;
        // 使用 do-while 避免处理 n=0 的边缘情况（n 从 1 到 2^31-1，代表 1 到 ZZZ）
        // (n - 1) % 26 将 1-26 映射到 0-25
        // n = (n - 1) / 26 在循环中递减 n
        do {
            n--;
            result += 'A' + (n % 26);
            n /= 26;
        } while (n > 0);

        reverse(result.begin(), result.end());
        return result;
    }
};
```

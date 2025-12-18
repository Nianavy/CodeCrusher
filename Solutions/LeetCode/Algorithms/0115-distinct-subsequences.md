# Question

[distinct-subsequences](https://leetcode.cn/problems/distinct-subsequences/)



# Answer

> solution

## 解决思路总结：不同子序列计数

### 1. 算法核心：动态规划 (DP)

这个问题无法用简单的双指针或贪心解决，因为子序列的匹配是**不连续**的，我们需要一种方法来系统地考虑所有可能性。动态规划是解决此类计数问题的标准工具。

### 2. DP 状态定义

我们定义一个二维数组 `f[i][j]`，其含义是：

> 在字符串 `s` 的前 `i` 个字符中，能组成字符串 `t` 的前 `j` 个字符的不同子序列的个数。

### 3. 状态转移方程

这是整个问题的核心。对于 `s[i]` 和 `t[j]`，我们有两种选择：

1.  **不使用 `s[i]` 来匹配 `t[j]`**：
    *   那么，`s` 的前 `i` 个字符能组成 `t` 的前 `j` 个字符的个数，就等于 `s` 的前 `i-1` 个字符能组成 `t` 的前 `j` 个字符的个数。
    *   公式：`f[i][j] = f[i-1][j]`

2.  **使用 `s[i]` 来匹配 `t[j]`**：
    *   这有一个前提：`s[i]` 必须等于 `t[j]`。
    *   如果匹配，那么除了 `s` 的前 `i-1` 个字符能组成 `t` 的前 `j` 个字符的个数 (`f[i-1][j]`)，我们还可以加上 `s` 的前 `i-1` 个字符能组成 `t` 的前 `j-1` 个字符的个数 (`f[i-1][j-1]`)。
    *   公式：如果 `s[i] == t[j]`，则 `f[i][j] += f[i-1][j-1]`

**最终状态转移方程：**

```
f[i][j] = f[i-1][j]
if (s[i] == t[j]) {
    f[i][j] += f[i-1][j-1]
}
```

### 4. 基线条件与初始化

*   **`f[i][0]`**：`t` 是一个空字符串。空字符串是任何字符串的子序列，且只有一种方式（即什么都不选）。因此，对于所有 `i`，`f[i][0] = 1`。
*   **`f[0][j]` (j > 0)**：`s` 是一个空字符串，而 `t` 非空。非空字符串不可能由空字符串的子序列组成。因此，对于所有 `j > 0`，`f[0][j] = 0`。

### 5. 关键陷阱：整数溢出

这是**工程实现中最重要的考虑因素**。

*   **问题**：子序列的数量可能是一个天文数字，远超 `int` (约 21 亿) 甚至 `long` (约 9e18) 的范围。
*   **后果**：如果使用 `int` 或 `long`，当数量超过其最大值时，会发生**整数溢出**，导致结果变为一个错误的负数或很小的正数，程序逻辑完全破坏。
*   **解决方案**：必须使用 `unsigned long long` (在 C++ 中) 或 `unsigned long long` (在 C 中)。这是 C/C++ 标准中能提供的最大标准整数类型，可以极大地降低溢出的风险。

### 6. 实用主义优化：空间优化

观察状态转移方程 `f[i][j]` 只依赖于 `f[i-1][j]` 和 `f[i-1][j-1]`。这意味着我们不需要保存整个二维 `dp` 表，只需要保存上一行的数据即可。

*   **方法**：将二维数组 `f[N+1][M+1]` 优化为一维数组 `f[M+1]`。
*   **关键技巧**：由于 `f[j]` 的新值依赖于 `f[j-1]` 的**旧值**，在遍历 `j` 时，必须**从后向前**进行。如果从前向后，`f[j-1]` 的值已经被更新为当前行的值，计算 `f[j]` 时就会出错。

**最终结论**：通过定义清晰的 DP 状态，构建正确的状态转移方程，并严格防范整数溢出，我们才能得到一个既正确又健壮的解决方案。空间优化则是锦上添花，体现了对内存的实用主义考量。

### Way Of C

> source code

```c
int numDistinct(char* s, char* t) {
    int n = strlen(s);
    int m = strlen(t);

    if (m == 0) return 1;
    if (n < m) return 0;

    // 关键：使用 unsigned long long 防止整数溢出
    unsigned long long *dp = (unsigned long long*)calloc(m + 1, sizeof(unsigned long long));
    if (!dp) return 0; // 内存分配失败

    // 基线条件
    dp[0] = 1;

    // DP 迭代
    for (int i = 0; i < n; ++i) {
        // **关键：反向遍历 j**
        for (int j = m; j > 0; --j) {
            if (s[i] == t[j - 1]) {
                dp[j] += dp[j - 1];
            }
        }
    }

    unsigned long long result = dp[m];
    free(dp);
    return (int)result; // 最终结果可能依然溢出 int，但题目通常保证不溢出
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int numDistinct(std::string s, std::string t) {
        int n = s.length();
        int m = t.length();

        // 空字符串 t 是任何字符串的子序列
        if (m == 0) return 1; 
        if (n < m) return 0;

        // 关键：使用 unsigned long long 防止整数溢出
        std::vector<unsigned long long> dp(m + 1, 0);

        // 基线条件：空字符串 t 是任何 s 的子序列
        dp[0] = 1;

        // DP 迭代
        for (int i = 0; i < n; ++i) {
            // **关键：反向遍历 j，避免覆盖 f[i-1][j-1]**
            // 因为 f[j] 的新值依赖于 f[j-1] 的旧值，所以从后往前更新
            for (int j = m; j > 0; --j) {
                if (s[i] == t[j - 1]) { // t is 0-indexed, dp is 1-indexed for j
                    dp[j] += dp[j - 1];
                }
                // 如果不匹配，dp[j] 保持不变，即等于上一轮的 dp[j]
            }
        }

        return dp[m];
    }
};
```

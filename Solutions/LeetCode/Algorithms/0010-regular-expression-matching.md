# Question

[regular-expression-matching](https://leetcode.cn/problems/regular-expression-matching/)



# Answer

> solution

**动态规划的教科书式应用：拆解问题，定义状态，然后用最直接的方式构建转移。**

1.  **明确状态定义：`dp[i][j]` 是核心。**
    *   它代表了**“`s` 的前 `i` 个字符是否能被 `p` 的前 `j` 个字符匹配？”**。这清晰地定义了每一个子问题。
    *   `i` 和 `j` 分别从 `0` 到 `n` 和 `m` 遍历，覆盖了所有可能的子字符串和子模式。

2.  **设定初始条件：一切从空开始。**
    *   `dp[0][0] = true`：空字符串 (`""`) 总是能匹配空模式 (`""`)。这是所有递归或 DP 的起点。
    *   **处理 `dp[0][j]`：** 当 `s` 为空 (`i=0`) 时，模式 `p` 只能通过 `char*` (即 `*` 匹配零次) 的形式来匹配空字符串。例如 `p="a*b*"` 可以匹配 `s=""`。这通过迭代 `p` 的前 `j` 个字符，判断 `p[j-1]` 是否为 `*`，并依赖 `dp[0][j-2]` 来实现。

3.  **核心状态转移：根据当前模式字符分类讨论。**
    *   **情况一：当前模式字符 `p[j-1]` 不是 `*` (即是普通字符 `a-z` 或 `.` )。**
        *   这时，`dp[i][j]` 为 `true` 的条件是：
            *   `s` 的当前字符 `s[i-1]` 能够匹配 `p` 的当前字符 `p[j-1]` (即 `s[i-1] == p[j-1]` 或 `p[j-1] == '.'`)。
            *   并且，`s` 的前 `i-1` 个字符已经能够匹配 `p` 的前 `j-1` 个字符 (即 `dp[i-1][j-1]` 为 `true`)。
        *   **逻辑：`dp[i][j] = (s[i-1] == p[j-1] || p[j-1] == '.') && dp[i-1][j-1]`**

    *   **情况二：当前模式字符 `p[j-1]` 是 `*`。**
        *   这时，`*` 总是和它前面的字符 `p[j-2]` 形成一个匹配单元 `(p[j-2]*)`。`*` 可以匹配零次或多次。
        *   **子情况 A：`*` 匹配零次。**
            *   这意味着 `p[j-2]*` 这个单元根本不匹配任何 `s` 的字符。所以，`dp[i][j]` 的真假取决于 `s` 的前 `i` 个字符是否能匹配**跳过整个 `p[j-2]*` 单元**后剩下的 `p` 的前 `j-2` 个字符。
            *   **逻辑：`dp[i][j] = dp[i][j-2]`**
        *   **子情况 B：`*` 匹配一次或多次。**
            *   这意味着 `p[j-2]*` 至少匹配了 `s` 的当前字符 `s[i-1]`。
            *   前提是 `s[i-1]` 能够匹配 `p[j-2]` (即 `s[i-1] == p[j-2]` 或 `p[j-2] == '.'`)。
            *   并且，**剩余的 `s` 的前 `i-1` 个字符** (`s[0...i-2]`) 能够匹配**完整的 `p` 的前 `j` 个字符** (`p[0...j-1]`)。这里的 `dp[i-1][j]` 表示 `*` 还在继续匹配 `s[0...i-2]`。
            *   **逻辑：`dp[i][j] = dp[i][j] || ((s[i-1] == p[j-2] || p[j-2] == '.') && dp[i-1][j])`**

**总之，核心思路就是：把一个大问题，分解成一系列相互依赖的小问题。通过表格记录每个小问题的答案，避免重复计算，最终推导出大问题的答案。这种方法，直接，高效，而且，一旦理解，就会发现其背后是简洁而优雅的逻辑。**

### Way Of C

> source code

```c
bool isMatch(char* s, char* p) {
    if (s == NULL || p == NULL) return false;

    int n = strlen(s);
    int m = strlen(p);

    bool **dp = (bool **)malloc((n + 1) * sizeof(bool *));
    if (dp == NULL) {
        fprintf(stderr, "E.\n");
        exit(EXIT_FAILURE);
    }

    for (int i = 0; i <= n; ++i) {
        dp[i] = (bool *)malloc((m + 1) * sizeof(bool));
        if (dp[i] == NULL) {
            fprintf(stderr, "E.\n");
            for (int k = 0; k < i; ++k) free(dp[k]);
            free(dp);
            exit(EXIT_FAILURE);
        }
        for (int j = 0; j <= m; ++j) dp[i][j] = false;
    }

    dp[0][0] = true;
    for (int j = 1; j <= m; ++j) {
        if (p[j - 1] == '*' && j >= 2) dp[0][j] = dp[0][j - 2];
    }

    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            char p_char = p[j - 1];
            if (p_char != '*') {
                if (s[i - 1] == p_char || p_char == '.') dp[i][j] = dp[i - 1][j - 1];
            }
            else {
                dp[i][j] = dp[i][j - 2];
                if (j >= 2 && (s[i - 1] == p[j - 2] || p[j - 2] == '.'))
                    dp[i][j] = dp[i][j] || dp[i - 1][j];
            }
        }
    }
    bool result = dp[n][m];
    for (int i = 0; i <= n; ++i) free(dp[i]);
    free(dp);
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        string processed_p;
        if (!p.empty()) {
            processed_p += p[0];
            for (size_t k = 1; k < p.length(); ++k) {
                if (p[k] == '*' && processed_p.back() == '*') {}
                else processed_p += p[k];
            }
        }
        p = processed_p;
        int n = s.length();
        int m = p.length();
        vector<vector<bool>> dp(n + 1, vector<bool>(m + 1, false));
        dp[0][0] = true;
        for (int j = 1; j <= m; ++j) {
            if (p[j - 1] == '*' && j >= 2) dp[0][j] = dp[0][j - 2];
        }
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= m; ++j) {
                char p_char = p[j - 1];
                if (p_char != '*') {
                    if (s[i - 1] == p_char || p_char == '.') dp[i][j] = dp[i - 1][j - 1];
                }
                else {
                    dp[i][j] = dp[i][j - 2];
                    if (j >= 2 && (s[i - 1] == p[j - 2] || p[j - 2] == '.'))
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                }
            }
        }
        return dp[n][m];
    }
};
```

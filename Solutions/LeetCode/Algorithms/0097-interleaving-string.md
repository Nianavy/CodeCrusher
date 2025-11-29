# Question

[interleaving-string](https://leetcode.cn/problems/interleaving-string/)



# Answer

> solution

### O($N^2$) DP 及空间优化

解决该问题的思路是构建一张 DP 表，并从左上角 $DP[0][0]$ 开始填充：

1.  **预处理**：首先检查长度：`len(s1) + len(s2) == len(s3)` 是必要条件。
2.  **空间优化**：如果 $len(s1) > len(s2)$，交换 $s1$ 和 $s2$，确保 DP 数组的维度最小。
3.  **初始化**：
    *   $DP[0][0] = \text{true}$ (空字符串可以交错组成空字符串)。
    *   初始化 $DP[0][j]$ 和 $DP[i][0]$：检查 $s2$ (或 $s1$) 在没有另一个字符串参与的情况下能否匹配 $s3$ 的前缀。
4.  **状态转移**：
    *   对于每个 $DP[i][j]$，检查两个方向的转移：
        *   **来自 $s1$**：如果 $DP[i-1][j]$ 为真，并且 $s1[i-1]$ 匹配 $s3[i+j-1]$。
        *   **来自 $s2$**：如果 $DP[i][j-1]$ 为真，并且 $s2[j-1]$ 匹配 $s3[i+j-1]$。
    *   最终结果由 $DP[len1][len2]$ 决定。

5.  **C 语言的内存管理**：如果在 C 语言中分配了 `table` 和 `dp` 两个堆内存块，则函数结束前必须精确地 `free(table)` 和 `free(dp)`，以杜绝内存泄漏。

### Way Of C

> source code

```c
/**
 * 判断 s3 是否由 s1 和 s2 交错组成 (2D DP 实现)。
 * 修复了内存泄漏问题。
 */
bool isInterleave(char* s1, char* s2, char* s3)
{
    int i, j;
    int len1 = strlen(s1);
    int len2 = strlen(s2);
    
    if (len1 + len2 != strlen(s3)) {
        return false;
    }

    bool result = false;
    bool *table = NULL; // 数据块
    bool **dp = NULL;   // 指针数组

    // 1. 分配内存
    // table: 连续的 N*M 空间
    table = (bool *)malloc((len1 + 1) * (len2 + 1) * sizeof(bool));
    // dp: N 个指针的空间
    dp = (bool **)malloc((len1 + 1) * sizeof(bool *));
    
    if (!table || !dp) {
        // 分配失败，必须释放已经分配的部分
        free(table);
        free(dp);
        return false;
    }

    // 设置指针数组，指向 table 的行（确保 O(1) 访问 dp[i][j]）
    for (i = 0; i < len1 + 1; i++) {
        dp[i] = &table[i * (len2 + 1)];
    }

    // 初始化 DP 表格为 false
    memset(table, 0, (len1 + 1) * (len2 + 1) * sizeof(bool));

    dp[0][0] = true;

    // 2. 初始化边界
    for (i = 1; i < len1 + 1; i++) {
        dp[i][0] = dp[i - 1][0] && s1[i - 1] == s3[i - 1];
    }
    for (j = 1; j < len2 + 1; j++) {
        dp[0][j] = dp[0][j - 1] && s2[j - 1] == s3[j - 1];
    }

    // 3. 状态转移
    for (i = 1; i < len1 + 1; i++) {
        for (j = 1; j < len2 + 1; j++) {
            char s3_char = s3[i + j - 1];
            
            // 来自上方 (s1[i-1] 匹配)
            bool up = dp[i - 1][j] && s1[i - 1] == s3_char;
            
            // 来自左方 (s2[j-1] 匹配)
            bool left = dp[i][j - 1] && s2[j - 1] == s3_char;
            
            dp[i][j] = up || left;
        }
    }

    result = dp[len1][len2];
    
    // 4. 释放内存。这是不可谈判的！
    free(table); // 先释放数据块
    free(dp);    // 再释放指针数组块
    
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 判断 s3 是否由 s1 和 s2 交错组成。
     * 使用 O(min(N, M)) 空间复杂度进行优化。
     */
    bool isInterleave(std::string s1, std::string s2, std::string s3) {
        int n = s1.size();
        int m = s2.size();
        
        if (s3.size() != n + m) return false;

        // 优化空间：确保 s1 (n) 始终是较短的字符串，以便 DP 数组更小。
        if (n > m) {
            return isInterleave(s2, s1, s3);
        }

        // dp[j] 存储 DP[i][j] 的状态，代表 s1[i] 和 s2[j] 能否组成 s3[i+j]
        std::vector<bool> dp(m + 1, false);

        // 初始状态: DP[0][0]
        dp[0] = true;

        // 初始化第 0 行 (s1 为空, i=0)
        for (int j = 1; j <= m; ++j) {
            // s2[j-1] 对应 s3[j-1]
            dp[j] = dp[j - 1] && (s2[j - 1] == s3[j - 1]);
        }

        // 填充 DP 表格 (i 从 1 到 n)
        for (int i = 1; i <= n; ++i) {
            
            // 初始化当前行的第 0 列 (j=0)
            // dp[0] 存储 DP[i][0] 的值 (s1[i-1] 对应 s3[i-1])
            dp[0] = dp[0] && (s1[i - 1] == s3[i - 1]);
            
            // 填充当前行的 j=1 到 m
            for (int j = 1; j <= m; ++j) {
                
                char s3_char = s3[i + j - 1];
                
                // 1. 来自上方 (DP[i-1][j]): 当前的 dp[j] 存储的是上一行 i-1 的状态
                bool from_s1 = dp[j] && (s1[i - 1] == s3_char);
                
                // 2. 来自左方 (DP[i][j-1]): 当前的 dp[j-1] 存储的是当前行 i 的状态
                bool from_s2 = dp[j - 1] && (s2[j - 1] == s3_char);
                
                // 更新 DP[i][j]
                dp[j] = from_s1 || from_s2;
            }
        }

        return dp[m];
    }
};
```

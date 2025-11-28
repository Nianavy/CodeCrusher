# Question

[unique-binary-search-trees](https://leetcode.cn/problems/unique-binary-search-trees/)



# Answer

> solution

### O($N^2$) 动态规划

解决该问题的思路是，迭代地构建 DP 数组，直到达到 $n$：

1.  **初始化**：
    *   创建一个大小为 $n+1$ 的 DP 数组 `dp`，并初始化 $DP[0] = 1$。
    *   使用 `long long` 类型。

2.  **外层循环**：
    *   迭代 $i$ 从 1 到 $n$ (计算 $DP[1], DP[2], \dots, DP[n]$)。

3.  **内层循环**：
    *   迭代 $j$ 从 0 到 $i-1$ (尝试所有可能的左子树节点数)。
    *   计算贡献：$DP[i] += DP[j] \times DP[i - 1 - j]$。

4.  **C 语言的铁律**：
    *   使用 `calloc` 或 `malloc` 分配的 DP 数组，在函数返回前必须使用 `free()` 释放，确保零内存泄漏。

### Way Of C

> source code

```c
/**
 * 计算可以构成独特二叉搜索树的总数 (卡特兰数 Cn)。
 * 使用 long long 以避免 n > 12 时的整数溢出。
 * 必须释放 malloc 的内存。
 */
long long numTrees(int n) {
    if (n < 0) return 0;
    
    // 1. 使用 long long 分配 DP 数组，防止溢出
    long long *sum = (long long *)calloc(n + 1, sizeof(long long)); 
    if (sum == NULL) {
        // 内存分配失败
        return 0; 
    }
    
    sum[0] = 1; // C0 = 1

    // 2. DP 状态转移
    for (int i = 1; i <= n; i++) {
        // j: 左子树节点数 (0 到 i-1)
        for (int j = 0; j < i; j++) {
            // 状态转移: sum[i] += sum[j] * sum[i - 1 - j]
            sum[i] += sum[j] * sum[i - 1 - j];
        }
    }
    
    long long result = sum[n];
    
    // 3. 释放内存。这是 C 语言中的绝对铁律。
    free(sum);
    
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
     * 计算可以构成独特二叉搜索树的总数 (卡特兰数 Cn)。
     * 使用 long long 以避免 n > 12 时的整数溢出。
     */
    long long numTrees(int n) {
        if (n < 0) return 0;
        
        // dp[i] 存储 i 个节点构成的 BST 总数 (Ci)
        // 初始化为 0，dp[0] 特殊处理
        std::vector<long long> dp(n + 1, 0); 
        dp[0] = 1; // C0 = 1 (空树)

        // i: 当前要计算的节点总数 (从 1 到 n)
        for (int i = 1; i <= n; i++) {
            // j: 左子树的节点数 (j = 0 到 i-1)
            for (int j = 0; j < i; j++) {
                // 状态转移公式: C[i] = SUM( C[j] * C[i - 1 - j] )
                // C[j] 是左子树的可能性
                // C[i - 1 - j] 是右子树的可能性 (i - 1 是总节点数减去根节点)
                dp[i] += dp[j] * dp[i - 1 - j];
            }
        }
        return dp[n];
    }
};
```

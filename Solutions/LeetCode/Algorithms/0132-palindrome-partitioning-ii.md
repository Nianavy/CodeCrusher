# Question

[palindrome-partitioning-ii](https://leetcode.cn/problems/palindrome-partitioning-ii/)



# Answer

> solution

【核心思路总结】

问题定义
将字符串切分，使得每个子串都是回文，求最少切几刀。本质是最短路径问题：把每个位置看作节点，如果 s[i..j] 是回文，则连一条从 i 到 j+1 的边，求从 0 到 n 的最短路径。

关键洞察

回文判断必须预计算：DP时反复判断回文会导致指数级重复计算
数据结构分离：回文表 f[i][j] 和 DP 数组 dp[i] 是两回事，不应该搅在一起
算法结构（两步走）

第一步：预计算回文表

<TEXT>
for (int i = n - 1; i >= 0; i--) {
    for (int j = i; j < n; j++) {
        f[i][j] = (s[i] == s[j]) && (j - i < 2 || f[i + 1][j - 1]);
    }
}
时间：O(n²)
空间：O(n²)
从后往前填表，利用已计算的子问题
第二步：一维 DP

<TEXT>
dp[0] = 0;
for (int i = 1; i < n; i++) {
    if (f[0][i]) dp[i] = 0;  // 整个前缀是回文
    else {
        dp[i] = i;  // 最坏情况：每个字符切一刀
        for (int j = 1; j <= i; j++) {
            if (f[j][i]) {
                dp[i] = min(dp[i], dp[j-1] + 1);
            }
        }
    }
}
dp[i] 表示前缀 s[0..i] 的最少切数
转移：如果 s[j..i] 是回文，则切在 j 前，dp[i] = dp[j-1] + 1
复杂度

时间：O(n²) + O(n²) = O(n²)
空间：O(n²) + O(n) = O(n²)
哲学总结

先定义数据：回文表是核心数据结构，比算法更重要
再设计流程：数据准备好了，DP就是直线逻辑
消除特殊情况：提前处理 i==j 和 len<2 的边界情况

### Way Of C

> source code

```c
int minCut(char* s) {
    int n = strlen(s);
    if (n <= 1) return 0;
    
    // 预计算回文表（紧凑存储）
    char (*f)[n] = malloc(n * n);
    for (int i = n - 1; i >= 0; i--) {
        for (int j = i; j < n; j++) {
            f[i][j] = (s[i] == s[j]) && (j - i < 2 || f[i + 1][j - 1]);
        }
    }
    
    // DP
    int* dp = malloc((n + 1) * sizeof(int));
    dp[0] = -1;  // 参考点
    
    for (int i = 1; i <= n; i++) {
        dp[i] = i - 1;  // 最大情况
        for (int j = 0; j < i; j++) {
            if (f[j][i - 1]) {  // s[j..i-1]是回文
                dp[i] = dp[i] < dp[j] + 1 ? dp[i] : dp[j] + 1;
            }
        }
    }
    
    int result = dp[n];
    free(f);
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
    int minCut(string s) {
        int n = s.size();
        if (n <= 1) return 0;
        
        // 预计算回文表
        vector<vector<bool>> f(n, vector<bool>(n, false));
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                f[i][j] = (s[i] == s[j]) && (j - i < 2 || f[i + 1][j - 1]);
            }
        }
        
        // DP
        vector<int> dp(n, n);  // 最大情况是n-1刀
        dp[0] = 0;
        
        for (int i = 1; i < n; i++) {
            if (f[0][i]) {  // 整个前缀是回文
                dp[i] = 0;
                continue;
            }
            for (int j = 1; j <= i; j++) {
                if (f[j][i]) {
                    dp[i] = min(dp[i], dp[j - 1] + 1);
                }
            }
        }
        
        return dp[n - 1];
    }
};
```

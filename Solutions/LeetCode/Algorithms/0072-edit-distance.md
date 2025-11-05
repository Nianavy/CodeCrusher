# Question

[edit-distance](https://leetcode.cn/problems/edit-distance/)



# Answer

> solution

### 1. 算法选择：动态规划 (DP)

*   **原则**：解决实际问题，避免理论上的花招。DP 是解决此问题的标准、清晰且高效（$O(N \cdot M)$）的方法。
*   **拒绝**：不采用 $O(N)$ 空间的滚动数组优化，因为其引入的复杂性严重降低了代码的可读性，维护成本高于内存收益。

### 2. 代码品味与结构标准

*   **简洁性**：函数体应短小精悍。状态转移方程应尽可能用统一的数学表达式表示，以消除不必要的 `if/else` 分支。
*   **数据结构**：数据结构应服务于代码简洁性。在 C++ 中通过修改输入字符串实现 1-based indexing 是“好品味”的表现。
*   **C 语言铁律**：在 C 代码中，内存管理必须是完美和明确的。任何 `malloc` 必须伴随 `free`。

---

## Ⅱ. 解决思路：动态规划 (DP) 状态转移

### 1. 状态定义

$f[i][j]$：将字符串 $A$ 的前 $i$ 个字符转换为字符串 $B$ 的前 $j$ 个字符所需的最小操作数。

### 2. 状态转移方程

$$f[i][j] = \min \begin{cases} f[i-1][j-1] + (A[i] \ne B[j]) & \text{（匹配/替换）} \\ f[i][j-1] + 1 & \text{（插入）} \\ f[i-1][j] + 1 & \text{（删除）} \end{cases}$$


### Way Of C

> source code

```c
// 简洁的 min 宏或内联函数是 C 编程的好品味
static inline int min_val(int a, int b)
{
    return a < b ? a : b;
}

/**
 * Levenshtein Distance (Edit Distance)
 * 最小编辑距离
 * 
 * @param word1 字符串1
 * @param word2 字符串2
 * @return 最小编辑距离
 */
int minDistance(char* word1, char* word2)
{
    int l1 = strlen(word1);
    int l2 = strlen(word2);
    
    // 快速路径：如果任何一个为空
    if (l1 == 0) return l2;
    if (l2 == 0) return l1;

    // DP 数组大小为 (l1 + 1) x (l2 + 1)
    int rows = l1 + 1;
    int cols = l2 + 1;
    
    // 1. 分配一个大的连续内存块 (用于 DP 表格数据)
    int *table = (int *)malloc(rows * cols * sizeof(int));
    // 2. 分配行指针 (用于模拟二维数组的访问)
    int **dp = (int **)malloc(rows * sizeof(int *));
    
    if (!table || !dp) {
        // 如果分配失败，必须清理已分配的部分
        free(dp); 
        free(table);
        return -1; // 返回错误代码
    }

    // 设置行指针，将二维索引映射到一维连续内存
    for (int i = 0; i < rows; i++) {
        dp[i] = table + i * cols;
    }

    // 边界初始化
    for (int i = 0; i < rows; i++) dp[i][0] = i;
    for (int j = 0; j < cols; j++) dp[0][j] = j;

    // DP 核心逻辑
    for (int i = 1; i < rows; i++) {
        for (int j = 1; j < cols; j++) {
            
            // 匹配/替换成本：0 如果字符相同，1 如果不同
            int cost = (word1[i - 1] != word2[j - 1]);
            
            int match_or_replace = dp[i - 1][j - 1] + cost;
            
            // 插入 (dp[i][j-1]) 或 删除 (dp[i-1][j])
            int insert_or_delete = min_val(dp[i - 1][j], dp[i][j - 1]) + 1;
            
            dp[i][j] = min_val(match_or_replace, insert_or_delete);
        }
    }

    int result = dp[l1][l2];

    // **CLEANUP**：释放所有分配的内存。这是强制性的。
    free(dp);
    free(table);

    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        // 使用 1-based indexing 简化 DP 边界处理
        string a = " " + word1;
        string b = " " + word2;

        int n = a.size(); // word1 长度 + 1
        int m = b.size(); // word2 长度 + 1

        // f[i][j] 存储 a[1..i] 到 b[1..j] 的最小编辑距离
        // 使用 vector<vector<int>> 比 C 风格数组更安全、更简洁
        vector<vector<int>> f(n, vector<int>(m));

        // 边界初始化：空字符串到任何字符串的距离是其长度
        for (int i = 0; i < n; ++i) f[i][0] = i; // 删除操作
        for (int j = 0; j < m; ++j) f[0][j] = j; // 插入操作

        // DP 核心循环
        for (int i = 1; i < n; ++i) {
            for (int j = 1; j < m; ++j) {
                // 1. 替换或匹配成本 (来自左上角)
                // cost 等于 0 (匹配) 或 1 (替换)
                int cost = (a[i] != b[j]);
                int replace_or_match = f[i - 1][j - 1] + cost;

                // 2. 插入/删除操作 (来自上方或左方) + 1
                int insert_or_delete = min(f[i - 1][j], f[i][j - 1]) + 1;

                // 最终结果取最小值
                f[i][j] = min(replace_or_match, insert_or_delete);
            }
        }

        return f[n - 1][m - 1];
    }
};
```

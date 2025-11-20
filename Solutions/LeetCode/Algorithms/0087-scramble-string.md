# Question

[scramble-string](https://leetcode.cn/problems/scramble-string/)



# Answer

> solution

### 带记忆化的递归 或 迭代 DP

我们通过定义一个状态来表示子问题是否可解：

$$
DP[len][i][j]
$$

*   **$len$**：子串的长度。
*   **$i$**：$s1$ 中子串的起始索引。
*   **$j$**：$s2$ 中子串的起始索引。

该状态的含义是：**“长度为 $len$ 的子串 $s1[i \dots i+len-1]$ 和 $s2[j \dots j+len-1]$ 是否是彼此的扰乱字符串。”**

### 状态转移（分割与检查）

对于任何长度 $len > 1$ 的子串，我们必须尝试所有可能的分割点 $k$ ($1 \le k < len$)，将子串分成两部分：左侧长度 $k$，右侧长度 $len-k$。

状态 $DP[len][i][j]$ 为真，当且仅当存在一个分割点 $k$，使得以下两种情况至少有一种成立：

#### 1. 不进行交换（No Swap）

$s1$ 的左右两部分分别对应 $s2$ 的左右两部分：

*   左侧匹配：$DP[k][i][j]$ **为真**
*   右侧匹配：$DP[len-k][i+k][j+k]$ **为真**

$$\text{或}$$

#### 2. 进行了交换（Swap）

$s1$ 的左右两部分交叉对应 $s2$ 的两部分：

*   左侧匹配：$s1$ 的左侧 ($k$) 匹配 $s2$ 的右侧 ($k$)：$DP[k][i][j + len - k]$ **为真**
*   右侧匹配：$s1$ 的右侧 ($len-k$) 匹配 $s2$ 的左侧 ($len-k$)：$DP[len-k][i+k][j]$ **为真**

### C++ 版本：迭代 DP 的优势

C++ 版本采用**迭代 DP**（Bottom-Up）。

1.  **初始化：** 从最小的长度 $len=1$ 开始，填入 $s1[i] == s2[j]$ 的结果。
2.  **递推：** 逐步增加 $len$，并遍历所有 $i, j$ 起始点。对于每个状态 $DP[len][i][j]$，它依赖于**更小的 $k$**（长度）的结果。
3.  **简洁性：** 避免了递归的堆栈开销和函数调用的损耗，控制流更加直接和清晰。

### C 版本：带记忆化的递归优势

C 版本采用**带记忆化的递归**（Top-Down）。

1.  **记忆表：** 使用全局三维数组 `memo` 初始化为 `-1`（未计算）。
2.  **调用：** 递归函数 `scramble_memo` 总是先检查 `memo[i][j][len]`。如果已计算，立即返回结果。
3.  **计算：** 如果未计算，则按上述状态转移逻辑进行递归调用，并在返回前将结果存入 `memo`。
4.  **实用主义：** 对于 C 语言环境，递归实现通常比手动维护三维数组索引的迭代版本更容易编写和调试。

### 共同的实用主义剪枝（Pruning）

两个版本都在开始时执行了 $O(N)$ 的**字符计数检查**：如果 $s1$ 和 $s2$ 所包含的字符集合和频率不一致，它们绝不可能是扰乱字符串。这避免了许多不必要的深度递归或 DP 计算，是**实用且高效**的第一道防线。

### Way Of C

> source code

```c
// 最大长度 N <= 30
#define MAX_LEN 30 

// memo 存储计算结果：0=false, 1=true, -1=未计算
// memo[i][j][len]
static int memo[MAX_LEN][MAX_LEN][MAX_LEN + 1];
static int G_LEN = 0; // 全局长度

/**
 * 避免重复计算，使用记忆化。
 * 简洁地传递指针和长度，而不是笨重的 low/high 索引。
 */
static bool scramble_memo(char *s1, int i, char *s2, int j, int len)
{
    // 基础情况 1: 长度为 1
    if (len == 1) {
        return s1[i] == s2[j];
    }
    
    // 基础情况 2: 检查记忆表
    if (memo[i][j][len] != -1) {
        return memo[i][j][len];
    }

    // 核心逻辑：尝试所有分割点 k
    for (int k = 1; k < len; k++) {
        
        // 分割成：(左 k, 右 len-k)
        
        // 方案 A: 未交换
        // s1 左侧 (i, k) vs s2 左侧 (j, k)
        // s1 右侧 (i+k, len-k) vs s2 右侧 (j+k, len-k)
        if (scramble_memo(s1, i, s2, j, k) &&
            scramble_memo(s1, i + k, s2, j + k, len - k)) {
            memo[i][j][len] = 1;
            return true;
        }

        // 方案 B: 交换
        // s1 左侧 (i, k) vs s2 右侧 (j + len - k, k)
        // s1 右侧 (i+k, len-k) vs s2 左侧 (j, len - k)
        if (scramble_memo(s1, i, s2, j + len - k, k) &&
            scramble_memo(s1, i + k, s2, j, len - k)) {
            memo[i][j][len] = 1;
            return true;
        }
    }
    
    // 失败，保存结果
    memo[i][j][len] = 0;
    return false;
}

bool isScramble(char* s1, char* s2) 
{
    int len1 = strlen(s1);
    int len2 = strlen(s2);
    
    if (len1 != len2) return false;
    if (len1 == 0) return true;
    
    G_LEN = len1;

    // 字符集检查：O(N) 剪枝
    int c1[128] = { 0 }, c2[128] = { 0 };
    for (int i = 0; i < len1; i++) {
        c1[s1[i]]++;
        c2[s2[i]]++;
    }
    if (memcmp(c1, c2, 128 * sizeof(int)) != 0) {
        return false;
    }

    // 重置记忆表，准备开始计算。
    // 警告：如果你在多线程环境使用，你需要动态分配或使用线程局部存储。
    // 对于 leetcode 这种单线程环境，memset 即可。
    memset(memo, -1, sizeof(memo));

    // 基础情况：如果两个字符串直接相等，就不用算了
    if (strcmp(s1, s2) == 0) {
        return true;
    }

    return scramble_memo(s1, 0, s2, 0, len1);
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 避免不必要的递归堆栈，使用清晰的迭代动态规划。
     * 复杂度: O(N^4)
     */
    bool isScramble(string s1, string s2) {
        int n = s1.size();
        if (n != s2.size()) return false;
        if (n == 0) return true;

        // 字符集检查：O(N)，比排序更快。
        array<int, 128> counts = {0};
        for (char c : s1) counts[c]++;
        for (char c : s2) counts[c]--;
        for (int count : counts) {
            if (count != 0) return false;
        }

        // dp[len][i][j] 表示：
        // 长度为 len 的子串 s1[i...i+len-1] 和 s2[j...j+len-1] 是否是扰乱字符串。
        // 由于N<=30，三维数组比unordered_map更快。
        // 注意：C++会默认初始化为 false/0
        vector<vector<vector<bool>>> dp(n + 1, vector<vector<bool>>(n, vector<bool>(n, false)));

        // 长度 len = 1 的基本情况
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                dp[1][i][j] = (s1[i] == s2[j]);
            }
        }

        // 迭代长度 len = 2 到 n
        for (int len = 2; len <= n; len++) {
            // i 是 s1 的起始索引
            for (int i = 0; i <= n - len; i++) {
                // j 是 s2 的起始索引
                for (int j = 0; j <= n - len; j++) {
                    
                    // k 是分割点，将 len 分为两部分：左侧长度 k，右侧长度 len - k
                    for (int k = 1; k < len; k++) {
                        
                        // 1. 不进行交换（No swap required）
                        // s1[i..i+k-1] 匹配 s2[j..j+k-1] (长度 k)
                        // s1[i+k..i+len-1] 匹配 s2[j+k..j+len-1] (长度 len-k)
                        bool not_swapped = dp[k][i][j] && dp[len - k][i + k][j + k];

                        // 2. 进行了交换 (Swapped)
                        // s1[i..i+k-1] 匹配 s2[j + len - k .. j + len - 1] (长度 k)
                        // s1[i+k..i+len-1] 匹配 s2[j .. j + len - k - 1] (长度 len-k)
                        bool swapped = dp[k][i][j + len - k] && dp[len - k][i + k][j];

                        if (not_swapped || swapped) {
                            dp[len][i][j] = true;
                            break; // 找到一个成功的分割点即可
                        }
                    }
                }
            }
        }

        return dp[n][0][0];
    }
};
```

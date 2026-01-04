# Question

[word-break](https://leetcode.cn/problems/word-break/)



# Answer

> solution

这是一个典型的**动态规划 (Dynamic Programming)** 问题。我们需要将原问题拆解为子问题，通过解决子问题来逐步构建原问题的解。

### 核心思路：能不能走到这里？

**定义状态**：
我们定义一个布尔数组 `dp`，其中 `dp[i]` 代表 **字符串 `s` 的前 `i` 个字符（即 `s[0...i-1]`）能否被字典中的单词完美分割**。

*   目标是求 `dp[n]`（`n` 是字符串长度），即整个字符串是否可分割。

**状态转移方程**：
`dp[i] = true` 当且仅当：
存在一个索引 `j` (`0 <= j < i`)，使得：
1.  `dp[j] == true` （说明 `s[0...j-1]` 已经能被分割，是合法的）
2.  并且，子串 `s[j...i-1` 是字典中的一个单词。

### 算法步骤

1.  **预处理**：将字典（`wordDict`）放入一个查找效率高的数据结构中。在这个问题中，字典可能包含重复单词，且我们需要频繁检查“子串是否在字典中”，所以：
    *   **C++**：使用 `std::unordered_set<string>`。
    *   **C**：直接遍历数组，配合 `strncmp` 比较子串。

2.  **初始化 DP 数组**：
    *   创建大小为 `n+1` 的数组 `dp`。
    *   `dp[0] = true`。这是一个**边界条件**，表示空字符串总是可被分割的（它让状态转移可以从第 0 个位置开始）。
    *   其余位置初始化为 `false`。

3.  **递推填表**：
    *   外层循环 `i` 从 1 遍历到 `n`（处理前 1 个字符、前 2 个字符...直到整个字符串）。
    *   内层循环寻找分割点 `j`，或者遍历字典中的单词。如果发现 `dp[j]` 为真且 `s[j...i-1]` 在字典中，就将 `dp[i]` 设为真。

4.  **返回结果**：
    *   返回 `dp[n]` 的值。

### 为什么这是最优解？
*   **鲁棒性**：直接处理字符串比较，不存在哈希碰撞导致的错误。
*   **清晰性**：逻辑纯粹是逻辑判断，没有“滚动哈希”这种需要凑常数的技巧。
*   **效率**：时间复杂度虽然理论是 $O(N^2 \times L)$，但在实际工程中，字典长度有限，且通过 `break` 优化，运行非常快。

### Way Of C

> source code

```c
bool wordBreak(char* s, char** wordDict, int wordDictSize) {
    int n = strlen(s);
    
    /* 1. 预处理字典单词长度，避免在循环中重复计算 */
    int *word_lens = (int*)malloc(sizeof(int) * wordDictSize);
    for (int i = 0; i < wordDictSize; i++) {
        word_lens[i] = strlen(wordDict[i]);
    }
    /* 2. 初始化 DP 表 */
    bool *dp = (bool*)malloc(sizeof(bool) * (n + 1));
    memset(dp, false, n + 1);
    dp[0] = true; // 空串
    /* 3. 双重循环填表 */
    for (int i = 1; i <= n; i++) {
        for (int j = 0; j < wordDictSize; j++) {
            int len = word_lens[j];
            // 边界检查：防止越界，且必须由合法位置转移而来
            if (len <= i && dp[i - len]) {
                // 比较子串
                if (strncmp(s + (i - len), wordDict[j], len) == 0) {
                    dp[i] = true;
                    break; // 找到即止
                }
            }
        }
    }
    /* 4. 清理内存 (这是 C 代码区别于脚本的关键) */
    bool result = dp[n];
    free(dp);
    free(word_lens);
    
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        // 1. 把字典存入哈希集合，方便 O(1) 查找
        unordered_set<string> dict(wordDict.begin(), wordDict.end());
        
        int n = s.size();
        // dp[i] 表示 s 的前 i 个字符能否被字典分割
        vector<bool> dp(n + 1, false);
        dp[0] = true; // 空字符串总是能被分割的
        // 2. 外层循环：填 dp 表
        for (int i = 1; i <= n; ++i) {
            // 3. 内层循环：查看之前的某个状态是否可达
            // 这里简单优化：只检查可能成为单词起点的位置
            for (const string& word : dict) {
                int len = word.size();
                // 如果当前单词长度比 s 长，或者延伸不到当前索引 i，跳过
                if (len <= i) {
                    // 检查从 i-len 到 i 的子串是否匹配该单词
                    // 并且检查起点 i-len 是否可达
                    if (dp[i - len] && s.compare(i - len, len, word) == 0) {
                        dp[i] = true;
                        break; // 找到一个匹配即可，不用继续查字典
                    }
                }
            }
        }
        return dp[n];
    }
};
```

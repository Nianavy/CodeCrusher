# Question

[word-break-ii](https://leetcode.cn/problems/word-break-ii/)



# Answer

> solution

这是一个回溯算法 (Backtracking) 结合 动态规划 (DP) 剪枝 的问题。核心难点在于：如何在不超时的前提下，生成所有可能的句子组合。

1. 核心策略：先判断，再搜索 (Prune then Search)
如果直接 DFS 搜索所有组合，在字典很大或字符串可被多重分割时，复杂度极高。

DP 预处理 (dp 数组)：
定义 dp[i]：表示子串 s[i...n-1] 是否可被分割。
从后往前推导。dp[i] = true 当且仅当 s[i...j-1] 是单词且 dp[j] = true。
这相当于在回溯前先做了一次“可行性检查”。如果 dp[i] 为 false，就根本不需要进入该层的 DFS。
2. 回溯搜索 (DFS)
定义函数 dfs(start, current_sentence)。
状态：start 表示当前处理的字符串起始位置。
递归：遍历字典中的所有单词。
如果 s 从 start 开始匹配该单词：
将单词加入 current_sentence。
递归调用 dfs(start + word_len, current_sentence)。
撤销选择 (Backtrack)：从 current_sentence 中移除该单词。
结果：当 start == s.length() 时，说明走到了字符串末尾，将拼接好的句子加入结果集。
3. 性能优化点 (Avoid Bad Taste)
避免字符串拷贝：不要在递归参数中传递 string path（这会导致每次递归都复制整个路径）。应该传递引用 vector<string>& path，只在进入递归前 push_back，退出后 pop_back。最终句子仅在找到解时拼接一次。
避免频繁切片：在尝试匹配单词时，优先使用 string::compare 或者 string::substr，但配合 DP 剪枝可以减少无效的切片操作。
4. 最终流程
初始化：字典存入 unordered_set。
DP 预热：计算 dp 数组，从后往前标记所有可能的起点。
DFS：从第 0 个位置开始，基于 dp 数组的指导进行回溯，收集所有解。
返回：结果集合。

### Way Of C

> source code

```c
TODO
```

---

### Way Of C++

> source code

```c++
class Solution {
    unordered_set<string> dict;
    vector<string> res;
    vector<bool> dp; // Helper for pruning
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        // 1. Build dictionary
        dict.insert(wordDict.begin(), wordDict.end());
        // 2. DP Pre-calculation (Pruning)
        int n = s.size();
        dp.assign(n + 1, false);
        dp[n] = true; // End is reachable
        for (int i = n - 1; i >= 0; --i) {
            for (const string& word : dict) {
                if (i + word.size() <= n && dp[i + word.size()] && 
                    s.substr(i, word.size()) == word) {
                    dp[i] = true;
                    break; // Found a valid continuation
                }
            }
        }
        // 3. Backtracking only if solutions exist
        if (dp[0]) {
            vector<string> current;
            backtrack(s, 0, current);
        }
        return res;
    }
    void backtrack(const string& s, int start, vector<string>& current) {
        // Base case: reached the end
        if (start == s.size()) {
            string sentence;
            for (const string& word : current) {
                if (!sentence.empty()) sentence += " ";
                sentence += word;
            }
            res.push_back(sentence);
            return;
        }
        // Try every word in dict
        for (const string& word : dict) {
            int len = word.size();
            // Optimization: Check bounds and DP result
            if (start + len <= s.size() && dp[start + len]) {
                // Check if prefix matches
                if (s.compare(start, len, word) == 0) {
                    current.push_back(word);
                    backtrack(s, start + len, current);
                    current.pop_back(); // Backtrack
                }
            }
        }
    }
};
```

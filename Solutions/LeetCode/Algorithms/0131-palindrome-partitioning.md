# Question

[palindrome-partitioning](https://leetcode.cn/problems/palindrome-partitioning/)



# Answer

> solution

【核心思路总结】

第一层：问题定义
将字符串拆分成所有可能的回文子串组合。本质是回溯搜索问题。

第二层：预处理（关键优化）
先用 O(n²) 预计算所有子串 f[i][j] 的回文性质，避免 DFS 中重复计算。

<TEXT>
f[i][j] = s[i] == s[j] && f[i+1][j-1]
从后往前填表，利用已计算结果。这比每次检查回文快一个数量级。

第三层：DFS 框架

<TEXT>
dfs(当前起始位置) {
    if (起始位置 == 字符串末尾) 记录答案
    
    for (int i = 起始位置; i < n; i++) {
        if (f[起始位置][i])  // 子串是回文
            path.push_back(s.substr(...))
            dfs(i + 1)  // 继续往后拆
            path.pop_back()
    }
}
第四层：边界处理

递归终止：start == n 表示拆分完成
每层枚举：从 start 开始的所有可能结尾 i
路径记录：path 存储当前拆分方案，最后拷贝到 ans
效率权衡

时间：O(2^n * n)，但回文剪枝减少了大量分支
空间：O(n²) 存表 + O(n) 递归栈
没有过度工程：直接回溯 + 剪枝，没有复杂数据结构

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
public:
    vector<vector<string>> partition(string s) {
        int n = s.size();
        vector<vector<bool>> f(n, vector<bool>(n, true));
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                f[i][j] = s[i] == s[j] && f[i + 1][j - 1];
            }
        }
        vector<vector<string>> ans;
        vector<string> path;
        dfs(s, 0, f, ans, path);
        return ans;
    }
    
    void dfs(string& s, int u, vector<vector<bool>>& f, 
             vector<vector<string>>& ans, vector<string>& path) {
        if (u == s.size()) {
            ans.push_back(path);
            return;
        }
        for (int i = u; i < s.size(); i++) {
            if (f[u][i]) {
                path.push_back(s.substr(u, i - u + 1));
                dfs(s, i + 1, f, ans, path);
                path.pop_back();
            }
        }
    }
};
```

# Question

[word-ladder-ii](https://leetcode.cn/problems/word-ladder-ii/)



# Answer

> solution

只要 两个哈希表：

dist[word] —— 到 beginWord 的最短步数
prev[word] —— 存储所有直接前驱（同一个最短距离）。
实现三步：

双向 BFS：从两端同时扩展，第一次相遇即得最短距离。
反向 DFS：沿 prev 递归即可一次生成所有最短路径，零回溯宏。
字符逐位枚举当作邻接表：整段逻辑纯粹 O(M×L×26) 完成，无链表魔法。

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
    vector<vector<string>> findLadders(string beginWord, string endWord,
                                       vector<string>& wordList) {
        vector<vector<string>> ans;
        unordered_set<string> dict(wordList.begin(), wordList.end());
        if (!dict.count(endWord)) return ans;

        unordered_map<string, int> dist{{beginWord, 0}};
        unordered_map<string, vector<string>> prev;
        queue<string> q{{beginWord}};
        dict.erase(beginWord);
        int len = beginWord.size(), step = 0;
        bool found = false;

        /* 一步 BFS：记录最短步数和父路径 */
        while (!q.empty() && !found) {
            ++step;
            int m = q.size();
            unordered_set<string> vis;
            for (int i = 0; i < m; ++i) {
                auto cur = q.front(); q.pop();
                for (int p = 0; p < len; ++p) {
                    for (char c = 'a'; c <= 'z'; ++c) {
                        if (c == cur[p]) continue;
                        string nxt = cur;
                        nxt[p] = c;
                        if (!dict.count(nxt)) continue;
                        if (dist.count(nxt)) {          // 已访问过
                            if (dist[nxt] == step) prev[nxt].push_back(cur);
                            continue;
                        }
                        vis.insert(nxt);
                        dist[nxt] = step;
                        prev[nxt] = {cur};
                        q.push(nxt);
                        if (nxt == endWord) found = true;
                    }
                }
            }
            for (auto& w : vis) dict.erase(w);
        }
        if (!found) return ans;

        /* 反向 DFS */
        vector<string> path = {endWord};
        auto dfs = [&](auto&& self, const string& cur) -> void {
            if (cur == beginWord) {
                ans.emplace_back(path.rbegin(), path.rend());
                return;
            }
            for (const string& p : prev[cur]) {
                path.push_back(p);
                self(self, p);
                path.pop_back();
            }
        };
        dfs(dfs, endWord);
        return ans;
    }
};
```

# Question

[word-ladder](https://leetcode.cn/problems/word-ladder/)



# Answer

> solution

【问题本质】
Word Ladder 是求最短路径问题。从 beginWord 出发，每次改变一个字母，找到 endWord 的最少步数。

【解决思路】

第一层：算法选择
BFS（广度优先搜索）— 天然找最短路径，无需额外优化。

第二层：数据结构

队列：存储待处理的单词和当前步数
访问标记：防止重复处理同一单词
字典：快速查询单词是否存在
第三层：核心逻辑

<TEXT>
1. 将 beginWord 入队，步数=1
2. 循环处理队列中的每个单词：
   - 逐个改变每个字母位置（a-z）
   - 检查改后的单词是否在字典中且未访问
   - 若是，标记为已访问，入队，步数+1
   - 若改后单词==endWord，返回步数
3. 队列空仍未找到，返回0
第四层：实现细节

C++：用 unordered_set 存字典和访问状态，queue 存(word, step)对

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
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> dict(wordList.begin(), wordList.end());
        if (!dict.count(endWord)) return 0;
        
        unordered_set<string> visited;
        queue<pair<string, int>> q;
        q.push({beginWord, 1});
        visited.insert(beginWord);
        
        int len = beginWord.size();
        while (!q.empty()) {
            auto [word, step] = q.front();
            q.pop();
            
            if (word == endWord) return step;
            
            for (int i = 0; i < len; i++) {
                char orig = word[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    if (c == orig) continue;
                    word[i] = c;
                    if (dict.count(word) && !visited.count(word)) {
                        visited.insert(word);
                        q.push({word, step + 1});
                    }
                }
                word[i] = orig;
            }
        }
        return 0;
    }
};
```

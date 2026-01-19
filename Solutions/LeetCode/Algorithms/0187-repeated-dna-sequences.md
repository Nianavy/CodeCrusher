# Question

[repeated-dna-sequences](https://leetcode.cn/problems/repeated-dna-sequences/)



# Answer

> solution

**核心思路：用位哈希替代字符串哈希，消除内存分配和复制。**

1. 每个字符（A/C/G/T）映射为2位，10个字符共20位，形成整数 `key`。
2. 用 `key` 代替字符串作为哈希表的键（C用数组，C++用 `unordered_set`）。
3. 遍历 \(s\) 计算滑动窗口的 `key`：
   - **第一次出现**：记录起始位置。
   - **第二次出现**：标记该 `key` 为重复，加入结果。
   - **后续出现**：忽略（避免重复结果记录）。
4. **后处理**：通过记录的重复 `key`，再次遍历 \(s\) 或使用映射回推出对应字符串（C++版直接用 `key` 查 `substr`，C版需要内部存储映射）。
5. **关键点**：避免了所有 `substr` 分配和复制，时间 O(N)，空间 O(min(N, 2^20))。

**简单来说**：**将字符串二进制化**，用整数快速比较和哈希。

### Way Of C

> source code

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
char** findRepeatedDnaSequences(char* s, int* returnSize) {
    *returnSize = 0;
    int len = strlen(s);
    if (len < 10) return NULL;
    
    // 碱基映射：A=0, C=1, G=2, T=3，每个用2位，10个共20位
    const int MAX_KEYS = 1 << 20; // 2^20 = 1,048,576
    int* visited = calloc(MAX_KEYS, sizeof(int)); // 记录状态：0=未访问, >0=起始位置, -1=已记录重复
    if (!visited) return NULL;
    
    const int MAX_N = 100000; // 题目最大不同子串数
    char** result = malloc(MAX_N * sizeof(char*));
    if (!result) {
        free(visited);
        return NULL;
    }
    int result_size = 0;
    
    for (int i = 0; i + 10 <= len; i++) {
        unsigned int key = 0;
        for (int j = 0; j < 10; j++) {
            char c = s[i + j];
            int val;
            switch (c) {
                case 'A': val = 0; break;
                case 'C': val = 1; break;
                case 'G': val = 2; break;
                case 'T': val = 3; break;
                default: val = 0; // 假设输入只含ATCG
            }
            key = (key << 2) | val;
        }
        int idx = visited[key];
        if (idx == 0) {
            visited[key] = i + 1; // 存储非零偏移
        } else if (idx != -1 && idx != i + 1) {
            // 发现重复，且之前已记录其他位置
            char* seq = malloc(11);
            if (!seq) {
                // 内存不足，清理已分配的结果
                for (int k = 0; k < result_size; k++) free(result[k]);
                free(result);
                free(visited);
                return NULL;
            }
            memcpy(seq, s + i, 10);
            seq[10] = '\0';
            result[result_size++] = seq;
            visited[key] = -1; // 标记已记录
        }
    }
    
    *returnSize = result_size;
    free(visited);
    if (result_size == 0) {
        free(result);
        return NULL;
    }
    // 注意：调用者需要释放 result 中的每个 string 和 result 本身
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        vector<string> res;
        unordered_set<string_view> seen;
        unordered_set<string_view> repeated;
        
        for (int i = 0; i + 10 <= s.size(); i++) {
            // 使用string_view避免复制（C++17）
            string_view sv(s.data() + i, 10);
            if (seen.count(sv)) {
                repeated.insert(sv);
            } else {
                seen.insert(sv);
            }
        }
        
        // 将string_view转换为string
        for (auto& sv : repeated) {
            res.push_back(string(sv));
        }
        return res;
    }
};
```

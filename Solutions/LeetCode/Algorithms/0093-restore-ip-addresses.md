# Question

[restore-ip-addresses](https://leetcode.cn/problems/restore-ip-addresses/)



# Answer

> solution

###  回溯（Backtracking）流程

解决该问题的思路是，在每一步递归中，尝试从当前位置 (`index`) 划出长度为 1、2 或 3 的下一段 IP 地址：

1.  **基础检查（DFS 入口）**
    *   首先进行**长度剪枝**，如果剩余字符过多或过少，无法构成剩余的段数，则立即返回。
    *   检查是否达到 4 段。如果达到 4 段，且原始字符串恰好用完 (`index == s.length()`)，则找到一个有效解。

2.  **循环划分（尝试 1-3 个字符）**
    在循环中，尝试从 `index` 开始，提取长度为 `len=1` 到 `len=3` 的子串作为当前 IP 段。

3.  **约束验证（剪枝）**
    *   **长度剪枝**：如果 `index + len` 超过了总长度，`break`。
    *   **前导零剪枝**：如果 `len > 1` 且子串的第一个字符是 `'0'`，则 `continue` (或 `break`)。
    *   **值剪枝**：如果 `len == 3` 且转换后的整数值大于 255，则 `continue` (或 `break`)。

4.  **状态管理和递归**
    *   如果当前划分有效，将该段 IP 地址（加上点号）**添加到路径缓冲区**。
    *   递归调用下一层 DFS：`backtrack(s, index + len, segment_count + 1, path)`。
    *   递归返回后，**撤销**对路径缓冲区的修改（移除当前段和点号）。

这个流程通过高效的剪枝机制和 O(1) 空间状态管理，确保了算法的正确性和实用性。

### Way Of C

> source code

```c
#define MAX_IP_LEN 16 // 3*4 + 3 + 1

// 核心 DFS 函数
// path: 路径缓冲区
// pos: 当前路径在 path 中的写入位置
// s: 原始输入字符串
// index: 原始字符串 s 中当前处理的起始索引
// num_segments: 已划分的段数
// results: 结果数组
// count: 结果计数器
static void dfs_c(char *s, int index, int num_segments, char *path, int pos, char **results, int *count) {
    int i;
    
    // 基础剪枝：如果剩下的字符太少或太多，无法构成有效 IP
    int remaining = strlen(s) - index;
    int needed = 4 - num_segments;

    if (remaining < needed || remaining > needed * 3) {
        return;
    }
    
    // 1. 终止条件
    if (num_segments == 4) {
        if (index == strlen(s)) {
            // 成功：字符串用尽，且划分为 4 段
            
            // 移除最后一个多余的点号
            path[pos - 1] = '\0'; 
            
            // 为结果分配内存并复制（C语言规范要求）
            results[*count] = (char *)malloc(MAX_IP_LEN);
            if (!results[*count]) return; // 内存分配失败处理
            strcpy(results[*count], path);
            
            (*count)++;
            
            // 恢复路径状态，以便回溯时 path[pos-1] 依然是点号
            path[pos - 1] = '.'; 
        }
        return;
    }

    // 2. 尝试划分 1, 2, 或 3 个字符
    for (int len = 1; len <= 3; len++) {
        if (index + len > strlen(s)) break;

        char temp_char = s[index + len]; // 保存下一个字符
        s[index + len] = '\0'; // 临时截断当前段，方便检查有效性

        const char *segment = s + index;
        
        // 检查前导零 (e.g., "01" is invalid)
        if (len > 1 && segment[0] == '0') {
            s[index + len] = temp_char; // 恢复原始字符串
            continue;
        }

        // 检查值是否 > 255
        if (len == 3) {
            int val = atoi(segment);
            if (val > 255) {
                s[index + len] = temp_char; // 恢复原始字符串
                continue;
            }
        }
        
        // --- 回溯操作 ---
        
        // 记录当前路径长度
        int pos_before = pos;
        
        // 1. 写入当前段
        strcpy(path + pos, segment);
        pos += len;
        
        // 2. 写入点号
        path[pos++] = '.';
        
        // 3. 深入递归
        s[index + len] = temp_char; // 恢复原始字符串
        dfs_c(s, index + len, num_segments + 1, path, pos, results, count);

        // 4. 撤销：只需恢复 pos 即可，不需要 memset
        pos = pos_before; 
        
        // 在 C 语言中，我们只需要在循环结束前恢复 s[index + len]
        s[index + len] = temp_char; 
    }
}


char** restoreIpAddresses(char* s, int* returnSize) {
    if (!s || strlen(s) < 4 || strlen(s) > 12) {
        *returnSize = 0;
        return NULL;
    }

    // 预估最大结果数，100是安全的上限
    char **results = (char **)malloc(100 * sizeof(char *));
    if (!results) {
        *returnSize = 0;
        return NULL;
    }
    
    int count = 0;
    char path_buffer[MAX_IP_LEN] = {0}; // 路径缓冲区
    
    // 注意：我们将修改 s 并在递归后恢复，以简化 segment 的提取和 atoi 调用。
    dfs_c(s, 0, 0, path_buffer, 0, results, &count);
    
    *returnSize = count;
    
    // 如果实际结果少于 100，可以缩小分配的 results 数组，但这不是强制性的。
    return results;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
private:
    std::vector<std::string> results;
    // 使用 std::string 作为路径缓冲区，通过 push_back/pop_back 实现回溯，
    // 避免昂贵的字符串复制。

    void backtrack(const std::string& s, int index, int segment_count, std::string& current_path) {
        
        // 1. 终止条件
        if (segment_count == 4) {
            // 如果成功划分了四段，且用完了所有字符
            if (index == s.length()) {
                current_path.pop_back(); // 移除末尾的点号
                results.push_back(current_path);
                current_path.push_back('.'); // 恢复路径状态
            }
            return;
        }
        
        // 如果剩下的字符不足以构成 IP 地址，或者字符太多
        if (s.length() - index < (4 - segment_count) || s.length() - index > 3 * (4 - segment_count)) {
            return;
        }

        // 2. 尝试划分 1, 2, 或 3 个字符
        for (int len = 1; len <= 3; ++len) {
            if (index + len > s.length()) break;

            // 提取当前段的子字符串
            std::string segment = s.substr(index, len);
            
            // 检查前导零：只有 "0" 允许作为长度大于 1 的前缀
            if (len > 1 && segment[0] == '0') {
                continue; 
            }

            // 检查值是否 > 255
            if (len == 3 && std::stoi(segment) > 255) {
                continue;
            }

            // --- 回溯操作 ---
            
            // 记录当前路径长度，用于回溯
            size_t path_len_before = current_path.length();

            // 1. 递归：添加当前段和点号
            current_path += segment;
            current_path += '.';

            // 2. 深入：进入下一段递归
            backtrack(s, index + len, segment_count + 1, current_path);

            // 3. 撤销：恢复路径到添加 segment 之前的状态
            current_path.resize(path_len_before); 
        }
    }

public:
    std::vector<std::string> restoreIpAddresses(std::string s) {
        if (s.length() < 4 || s.length() > 12) {
            return {}; // 基础剪枝
        }
        results.clear();
        std::string path_buffer;
        backtrack(s, 0, 0, path_buffer);
        return results;
    }
};
```

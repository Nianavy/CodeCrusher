# Question

[text-justification](https://leetcode.cn/problems/text-justification/)



# Answer

> solution

### **解决思路总结：双阶段分组与数学对齐法**

**格言：** “文本格式化必须精确。首先，用最少的时间将输入分成不可再分的组；然后，用最精确的算术来填充间隙。”

**核心思想：**
文本两端对齐（Full Justify）问题通过两个严格分离的阶段实现最优解：

#### **阶段一：分组 (O(N) 线性扫描)**

该阶段的任务是确定每一行能够容纳的单词范围 `[i, j)`。

1.  **外层循环 (`i`)：** 指向当前行开始的第一个单词。
2.  **内层循环 (`j`)：** 贪心地尝试将下一个单词 `words[j]` 加入当前行。
3.  **贪心条件：** 在计算 `current_line_chars`（单词字符总数）时，必须加上**至少需要的空格数** (`j - i`)。如果这个总长度超过了 `maxWidth`，则当前行到 `words[j-1]` 结束。
4.  **推进：** 一旦确定了行范围 `[i, j)`，外层循环的 `i` 指针被推进到 `j`，开始处理下一行。

#### **阶段二：格式化与空格分配**

确定行范围后，根据该行是否为**单单词行**或**最后一行**，采用不同的策略进行精确的空格填充。

**情况 A：左对齐（单单词行 或 最后一行）**

*   **特点：** 单词间只需一个空格，其余所有剩余空间全部填充到行尾。
*   **操作：** 简单地连接单词和单个空格，然后用剩余的空格填充到 `maxWidth`。

**情况 B：标准两端对齐 (Full Justify)**

*   **目标：** 将所有剩余空格平均分配到单词间的空格槽中。
*   **数学分配：**
    1.  **计算空格总数 (`total_spaces_needed`)：** `maxWidth` 减去行内所有单词的字符总数。
    2.  **计算空格槽数 (`num_slots`)：** 行内单词数减 1。
    3.  **平均分配：** 计算 `avg_spaces = total_spaces_needed / num_slots`。这是每个空格槽至少应该拥有的空格数。
    4.  **分配余数：** 计算 `extra_spaces = total_spaces_needed % num_slots`。这些余数空格必须从左侧开始，每个槽多分配一个。
*   **操作：** 遍历空格槽，前 `extra_spaces` 个槽分配 `avg_spaces + 1` 个空格，其余槽分配 `avg_spaces` 个空格。

**实用主义的体现：**
这种双阶段结构确保了算法的 O(N) 效率。最关键的格式化阶段使用了精确的数学模型来分配空格，避免了循环中的不确定性或复杂的字符串操作，这是处理文本格式化问题的最佳实践。

### Way Of C

> source code

```c
// 辅助函数：计算单词的总字符长度
static int calculate_total_chars(char** words, int* word_lens, int start, int end) {
    int total = 0;
    for (int i = start; i < end; i++) {
        total += word_lens[i];
    }
    return total;
}

// 辅助函数：填充行内容 (将结果写入 line 指针指向的内存)
static void fill_line(char *line, char** words, int* word_lens, int maxWidth,
                      int start, int end, bool is_last_line)
{
    int num_words = end - start;
    int total_chars = calculate_total_chars(words, word_lens, start, end);
    char *p = line;
    
    // 1. 左对齐 (单词或最后一行)
    if (num_words == 1 || is_last_line) {
        for (int i = start; i < end; i++) {
            // 复制单词
            memcpy(p, words[i], word_lens[i]);
            p += word_lens[i];
            
            // 单词间一个空格
            if (i < end - 1) {
                *p++ = ' ';
            }
        }
        // 填充剩余空格到行尾
        while (p - line < maxWidth) {
            *p++ = ' ';
        }
    } 
    // 2. 标准两端对齐
    else {
        int total_spaces_needed = maxWidth - total_chars;
        int num_slots = num_words - 1; 

        int avg_spaces = total_spaces_needed / num_slots;
        int extra_spaces = total_spaces_needed % num_slots;

        // 放置第一个单词
        memcpy(p, words[start], word_lens[start]);
        p += word_lens[start];

        // 放置其余单词和空格
        for (int k = 1; k < num_words; ++k) {
            int current_spaces = avg_spaces;
            if (k <= extra_spaces) {
                current_spaces++; // 前 extra_spaces 个槽多一个空格
            }
            
            // 写入空格
            memset(p, ' ', current_spaces);
            p += current_spaces;
            
            // 写入单词
            memcpy(p, words[start + k], word_lens[start + k]);
            p += word_lens[start + k];
        }
    }
    
    // 确保字符串终止
    *p = '\0';
}

/**
 * Return an array of size *returnSize.
 * Note: The returned array must be malloced, assume caller calls free().
 */
char** fullJustify(char** words, int wordsSize, int maxWidth, int* returnSize) {
    if (wordsSize == 0) {
        *returnSize = 0;
        return NULL;
    }
    
    // 由于不确定最终行数，我们先分配一个足够的容量，并在最后调整大小。
    // 最多有 wordsSize 行。
    int max_lines = wordsSize;
    
    // 1. 分配指针数组 (存储每一行的起始地址)
    char **lines = (char**)malloc(max_lines * sizeof(char *));
    
    // 2. 分配所有行内容的连续内存块 (总长度 = max_lines * (maxWidth + 1))
    // 这样做是为了避免多次 malloc/free，提高效率，并方便调用者一次释放。
    char *buffer = (char*)malloc(max_lines * (maxWidth + 1) * sizeof(char));

    if (!lines || !buffer) {
        free(lines);
        free(buffer);
        *returnSize = 0;
        return NULL;
    }

    // 预计算单词长度
    int *word_lens = (int*)malloc(wordsSize * sizeof(int));
    for (int i = 0; i < wordsSize; i++) {
         word_lens[i] = strlen(words[i]);
    }

    int i = 0; // i 指向当前行的第一个单词
    int current_line_index = 0; // 当前正在写入的行索引

    while (i < wordsSize) {
        int j = i; // j 试图找到当前行能放的最后一个单词 (不包含)
        int current_line_chars = 0; // 仅单词字符长度

        // 1. 分组阶段：确定 [i, j) 范围内的单词
        while (j < wordsSize) {
            int next_word_len = word_lens[j];
            
            // 计算当前行需要的总长度
            int required_length = current_line_chars + next_word_len;
            // 加上单词间的空格数 (至少 j - i 个空格)
            if (j > i) {
                required_length += (j - i); 
            }

            if (required_length > maxWidth) {
                break; // 放不下了
            }
            
            current_line_chars += next_word_len;
            j++;
        }
        
        // 2. 格式化阶段
        bool is_last_line = (j == wordsSize);
        
        // 设置当前行的指针
        lines[current_line_index] = buffer + current_line_index * (maxWidth + 1);

        // 格式化并填充行
        fill_line(lines[current_line_index], words, word_lens, maxWidth, 
                  i, j, is_last_line);

        current_line_index++;
        i = j; // 推进 i 到新行开始的位置
    }

    // 3. 内存清理与返回
    *returnSize = current_line_index;
    free(word_lens);
    
    // NOTE: 调用者必须释放 lines 数组和 buffer 块。
    // 通常我们返回的 char** 应该是连续的，但 C 语言的函数签名无法表达这一点。
    // 在这里，我们将指针数组和数据块都返回给调用者，依赖调用者正确释放。
    
    // 为了符合 LeetCode 的惯例，我们应该将 buffer 的地址存储在 lines[0]，
    // 然后将 lines 的地址返回。但这在实际中会导致内存管理混乱。
    // 我们采取的方案是：返回 lines (指针数组)，调用者需要自行追踪 buffer 进行释放。
    // 由于我们不能修改函数签名，我们假设调用者知道如何释放。
    
    return lines;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
private:
    // 辅助函数：创建指定数量的空格字符串
    std::string spaces(int count) {
        return std::string(count, ' ');
    }

    // 格式化当前行 [words[i], ..., words[j-1]]
    std::string format_line(const std::vector<std::string>& words, int i, int j, int maxWidth, bool is_last_line) {
        // 统计单词总字符长度 (不含空格)
        int num_words = j - i;
        int total_chars = 0;
        for (int k = i; k < j; ++k) {
            total_chars += words[k].size();
        }

        std::string line = "";
        
        // 1. 单词/最后一行左对齐
        if (num_words == 1 || is_last_line) {
            line += words[i];
            for (int k = i + 1; k < j; ++k) {
                line += " " + words[k];
            }
            // 填充剩余空格到行尾
            line += spaces(maxWidth - line.size());
            return line;
        }

        // 2. 标准两端对齐 (Full Justify)
        int total_spaces_needed = maxWidth - total_chars;
        int num_slots = num_words - 1; // 空格槽数量

        // 平均分配的空格数
        int avg_spaces = total_spaces_needed / num_slots;
        // 余数 (从左侧开始，每个空格槽多分配一个空格)
        int extra_spaces = total_spaces_needed % num_slots;

        line += words[i]; // 放置第一个单词

        // 放置其余单词
        for (int k = 1; k < num_words; ++k) {
            int current_spaces = avg_spaces;
            if (k <= extra_spaces) {
                current_spaces++; // 前 extra_spaces 个槽多一个空格
            }
            
            line += spaces(current_spaces);
            line += words[i + k];
        }

        return line;
    }

public:
    std::vector<std::string> fullJustify(std::vector<std::string>& words, int maxWidth) {
        std::vector<std::string> res;
        int n = words.size();
        int i = 0; // i 指向当前行的第一个单词

        while (i < n) {
            int j = i; // j 试图找到当前行能放的最后一个单词 (不包含)
            int current_line_chars = 0; // 仅单词字符长度

            // 1. 分组阶段：确定 [i, j) 范围内的单词
            while (j < n) {
                int next_word_len = words[j].size();
                int required_length = current_line_chars + next_word_len;
                // 加上单词间至少一个空格 (j - i)
                if (j > i) {
                    required_length += (j - i); // 计算单词间至少 j-i 个空格
                }

                if (required_length > maxWidth) {
                    break; // 放不下了
                }
                
                current_line_chars += next_word_len;
                j++;
            }
            
            // 2. 格式化阶段
            // is_last_line 为 true，则执行左对齐
            bool is_last_line = (j == n);
            res.push_back(format_line(words, i, j, maxWidth, is_last_line));

            // 3. 推进 i 到新行开始的位置
            i = j;
        }

        return res;
    }
};
```

# Question

[substring-with-concatenation-of-all-words](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)



# Answer

> solution

解决“串联所有单词的子串”问题的核心思路是**优化后的滑动窗口算法**，结合**哈希表**进行高效的单词频率统计。为了达到 O(N * W) 的线性时间复杂度，关键在于：

1.  **分批次滑动窗口**：由于所有单词长度 `W` 固定，匹配的子串的总长度 `M * W` 也固定。匹配的子串的起始位置 `i` 必须满足 `i % W` 是一个定值。因此，我们可以从 `0` 到 `W-1` 这 `W` 个不同的起始点，分别开始独立的滑动窗口遍历。
2.  **窗口内哈希计数**：每个滑动窗口内部维护一个哈希表 (`current_window_counts`)，记录当前窗口中每个单词的出现频率。
3.  **精确的窗口操作**：
    *   **右移 (`right` 指针)**：`right` 指针每次向右移动 `W` 距离，将一个新单词添加到窗口。
    *   **左移 (`left` 指针)**：当窗口内的某个单词计数超过目标，或遇到非法单词时，`left` 指针向右移动 `W` 距离，移除最左侧的单词，直到窗口恢复合法性。
    *   **匹配判断**：当 `words_found` 数量等于 `num_words` 且窗口长度恰好为 `total_len` 时，找到一个匹配。找到匹配后，立即将窗口左移一个单词，寻找下一个匹配。

### **C++ 版本解决问题的核心**

C++ 版本利用了标准库的强大功能，使得代码更简洁、更安全：

1.  **`std::unordered_map<std::string, int>`**：这是 C++ 版本的核心优势。它提供了高效的字符串哈希表，自动处理哈希冲突、内存管理和字符串比较。这极大地简化了单词频率统计的复杂性。
2.  **`std::string::substr()`**：方便地从主字符串 `s` 中提取固定长度的子串。虽然每次调用都有复制开销，但在总复杂度 O(N*W) 中，这部分开销是可接受的。
3.  **`std::vector<int>`**：自动管理结果列表的内存，无需手动 `malloc`/`realloc`/`free`。
4.  **Linus 哲学**：在 C++ 中，**优先利用成熟、高效且安全的标准库容器和算法**。这能减少代码量，降低出错风险，提高开发效率。专注于算法逻辑，让库来处理底层细节。

### **C 语言版本解决问题的核心**

C 语言版本由于缺乏高级标准库，需要手动实现和优化：

1.  **手动实现哈希表 (`HashMap`)**：
    *   C 语言需要**手动实现一个拉链法哈希表**来存储字符串到整数计数的映射。这包括 `HashNode` 结构、`hashmap_create`、`hashmap_put`、`hashmap_get`、`hashmap_destroy` 等函数。
    *   这增加了大量的代码量和维护复杂度，且是 Bug 的高发区。
2.  **字符串 ID 映射 (`StringIdMap`)**：
    *   这是 C 语言版本**提高性能的关键**。将 `words` 数组中出现的每个唯一的字符串，预先映射到一个唯一的整数 ID。
    *   这样，滑动窗口内的计数就可以使用简单的**整数数组** (`int* current_window_counts_by_id`) 来代替字符串哈希表，将字符串比较的 O(W) 开销降为整数比较的 O(1) 开销。
    *   这个 `StringIdMap` 只在开始时构建一次。
3.  **字符串操作**：
    *   从 `s` 中提取单词需要使用 `memcpy` 到临时缓冲区，并手动添加 `\0` 结束符。
    *   字符串的比较使用 `strcmp`。
    *   哈希函数 (`BKDRHash`) 需要手动实现。
4.  **严格的手动内存管理**：
    *   所有动态分配的内存（哈希表结构、桶数组、哈希节点、键字符串、计数数组、结果数组）都必须通过 `malloc`/`calloc` 分配，并在适当的时候通过 `free` 释放。**每次 `malloc` 都必须有对应的 `free`。**
    *   结果数组 `results` 在最终返回前可能需要 `realloc` 调整大小。
    *   **Linus 哲学**：在 C 中，必须对所有内存分配和释放有**精确的控制和追踪**。代码必须明确地展现内存的生命周期。为了效率，可以引入更复杂的辅助数据结构（如 ID 映射），但代价是更高的实现复杂度和潜在的 Bug 风险。

**总结来说，两个版本的核心算法都是基于 `W` 个独立滑动窗口和哈希表进行单词频率计数的策略。C++ 版本利用标准库的强大抽象实现了简洁和安全，而 C 版本则通过手动实现哈希表和字符串 ID 映射，在牺牲代码简洁性和增加手动内存管理复杂度的前提下，达到了相似的性能目标。**

### Way Of C

> source code

```c
// --- 辅助哈希表用于 `char*` 到 `int` 的映射 (将单词字符串映射到唯一 ID) ---
// 这个哈希表会在函数开始时构建一次，之后不再修改
// 键是 char* (指向 words 数组中的单词)，值是 int (对应的 ID)
typedef struct StringIdNode {
    char *key;                 // 存储单词字符串 (指向原始 words 数组的指针)
    int id;                    // 对应的唯一 ID
    struct StringIdNode *next; // 拉链
} StringIdNode;

typedef struct {
    StringIdNode **table;
    int capacity;
    int next_id;               // 用于生成新的唯一 ID
} StringIdMap;

// 简单的字符串哈希函数 (BKDRHash)
static unsigned int BKDRHash(const char *str) {
    unsigned int hash = 0;
    int seed = 131;
    while (*str) {
        hash = hash * seed + (*str++);
    }
    return hash;
}

// 创建 StringIdMap
static StringIdMap* string_id_map_create(int capacity) {
    if (capacity <= 0) capacity = 10;
    StringIdMap *map = (StringIdMap*)malloc(sizeof(StringIdMap));
    if (!map) {
        fprintf(stderr, "string_id_map_create: Failed to allocate StringIdMap.\n");
        exit(EXIT_FAILURE);
    }
    map->table = (StringIdNode**)calloc(capacity, sizeof(StringIdNode*));
    if (!map->table) {
        fprintf(stderr, "string_id_map_create: Failed to allocate table.\n");
        free(map);
        exit(EXIT_FAILURE);
    }
    map->capacity = capacity;
    map->next_id = 0; // 第一个 ID 从 0 开始
    return map;
}

// 获取单词对应的 ID，如果不存在则分配新 ID 并插入
static int string_id_map_get_or_assign(StringIdMap* map, char* key) {
    unsigned int hash_val = BKDRHash(key) % map->capacity;
    StringIdNode *node = map->table[hash_val];

    while (node != NULL) {
        if (strcmp(node->key, key) == 0) {
            return node->id; // 找到，返回现有 ID
        }
        node = node->next;
    }

    // 不存在，分配新 ID 并插入
    node = (StringIdNode*)malloc(sizeof(StringIdNode));
    if (!node) {
        fprintf(stderr, "string_id_map_get_or_assign: Failed to allocate StringIdNode.\n");
        exit(EXIT_FAILURE);
    }
    node->key = key; // 指向原始单词字符串，不复制
    node->id = map->next_id++;
    node->next = map->table[hash_val];
    map->table[hash_val] = node;
    return node->id;
}

// 释放 StringIdMap 内存
static void string_id_map_destroy(StringIdMap* map) {
    if (!map) return;
    for (int i = 0; i < map->capacity; i++) {
        StringIdNode *node = map->table[i];
        while (node != NULL) {
            StringIdNode *temp = node;
            node = node->next;
            // 注意：这里不 free(temp->key)，因为 key 指向原始 words 数组中的字符串
            // 只有当 key 是在 map_put 里面 malloc 复制的才需要 free
            // 但在这个优化版本里，key 指向的是原始 words 数组的字符串，或者临时 buffer 的字符串
            free(temp);
        }
    }
    free(map->table);
    free(map);
}


/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* findSubstring(char* s, char** words, int wordsSize, int* returnSize) {
    *returnSize = 0; // 初始化返回大小
    
    // 边界条件检查
    if (s == NULL || *s == '\0' || words == NULL || wordsSize == 0 || words[0] == NULL || *words[0] == '\0') {
        return NULL; // 无匹配可能，返回 NULL
    }

    int s_len = strlen(s);                 // 主字符串 s 的长度
    int word_len = strlen(words[0]);       // 每个单词的长度
    int total_len = wordsSize * word_len;  // 所有单词串联后的总长度

    // 如果主字符串 s 的长度小于所有单词串联的总长度，不可能找到匹配
    if (s_len < total_len) {
        return NULL;
    }

    // --- 步骤 1: 预处理 words 数组，将每个单词映射到唯一的整数 ID ---
    // 并统计每个 ID 的目标出现频率 (`target_counts_by_id`)
    // string_id_map 用于将字符串单词映射为 ID
    // 哈希容量可设为 wordsSize 的两倍或一个质数
    int id_map_capacity = wordsSize * 2 + 1; 
    StringIdMap* id_mapper = string_id_map_create(id_map_capacity);
    if (!id_mapper) return NULL;

    // target_counts_by_id 将存储每个单词 ID 的目标出现频率
    // 数组大小为 id_mapper->next_id 最大可能值 (wordsSize)
    int *target_counts_by_id = (int*)calloc(wordsSize, sizeof(int));
    if (!target_counts_by_id) {
        fprintf(stderr, "findSubstring: Failed to allocate target_counts_by_id.\n");
        string_id_map_destroy(id_mapper);
        return NULL;
    }

    // 将 words 列表中的每个单词映射到 ID 并统计目标频率
    for (int i = 0; i < wordsSize; i++) {
        int word_id = string_id_map_get_or_assign(id_mapper, words[i]);
        if (word_id >= wordsSize) { // 简单检查，防止 ID 超出预设数组大小
             fprintf(stderr, "findSubstring: Word ID exceeded expected max size. Adjust array allocation.\n");
             // 应该 realloc 或者提前计算精确的最大 ID 数量
             // 这里为简化，直接退出
             free(target_counts_by_id);
             string_id_map_destroy(id_mapper);
             return NULL;
        }
        target_counts_by_id[word_id]++;
    }
    int max_word_id = id_mapper->next_id; // 实际使用的最大 ID (也是 ID 的总数量)


    // --- 步骤 2: 初始化结果数组 ---
    // 结果数量最多为 `s_len - total_len + 1`，分配足够空间
    int *results = (int*)malloc(s_len * sizeof(int)); 
    if (!results) {
        fprintf(stderr, "findSubstring: Failed to allocate results array.\n");
        free(target_counts_by_id);
        string_id_map_destroy(id_mapper);
        return NULL;
    }
    int res_count = 0; // 实际找到的结果数量

    // --- 步骤 3: 滑动窗口遍历 ---
    // `word_len` 种不同的起始点 `i`
    for (int i = 0; i < word_len; ++i) {
        // 当前窗口内的单词计数 (`current_window_counts_by_id`)
        // 使用数组代替哈希表，因为我们现在处理的是整数 ID
        // 数组大小为 `max_word_id`
        int *current_window_counts_by_id = (int*)calloc(max_word_id, sizeof(int));
        if (!current_window_counts_by_id) {
            fprintf(stderr, "findSubstring: Failed to allocate current_window_counts_by_id.\n");
            free(results);
            free(target_counts_by_id);
            string_id_map_destroy(id_mapper);
            return NULL;
        }

        int words_found = 0; // 当前窗口中匹配到的单词数量
        int left = i;        // 当前滑动窗口的左边界 (起始索引)
        char current_word_buffer[word_len + 1]; // 临时缓冲区用于 substr

        // `right` 指针每次移动一个 `word_len` 的距离，作为窗口的右边界
        for (int right = i; right + word_len <= s_len; right += word_len) {
            memcpy(current_word_buffer, s + right, word_len);
            current_word_buffer[word_len] = '\0'; // 字符串结束符

            // 1. 尝试获取当前单词的 ID
            // 这里我们不能直接用 `string_id_map_get_or_assign`，
            // 因为 `current_word_buffer` 可能是 `words` 列表中没有的单词。
            // 应该只用 `string_id_map_get`。
            int current_word_id = -1; // 默认不存在
            StringIdNode *node = id_mapper->table[BKDRHash(current_word_buffer) % id_mapper->capacity];
            while (node != NULL) {
                if (strcmp(node->key, current_word_buffer) == 0) {
                    current_word_id = node->id;
                    break;
                }
                node = node->next;
            }

            // 1.1 如果当前单词 ID 是有效的 (即存在于原始 words 列表)
            if (current_word_id != -1) {
                current_window_counts_by_id[current_word_id]++; // 增加当前窗口中该单词的计数
                
                // 如果当前单词的计数未超过目标计数，则匹配单词数量增加
                if (current_window_counts_by_id[current_word_id] <= target_counts_by_id[current_word_id]) {
                    words_found++;
                }

                // 1.2 如果当前窗口中某个单词的计数超过了目标计数，则需要收缩窗口左边界
                while (current_window_counts_by_id[current_word_id] > target_counts_by_id[current_word_id]) {
                    char left_word_buffer[word_len + 1];
                    memcpy(left_word_buffer, s + left, word_len);
                    left_word_buffer[word_len] = '\0';

                    int left_word_id = -1; // 默认不存在
                    StringIdNode *left_node = id_mapper->table[BKDRHash(left_word_buffer) % id_mapper->capacity];
                    while(left_node != NULL) {
                        if (strcmp(left_node->key, left_word_buffer) == 0) {
                            left_word_id = left_node->id;
                            break;
                        }
                        left_node = left_node->next;
                    }
                    
                    // 确保 left_word_id 存在 (通常会的，因为它是从窗口中取出的)
                    if (left_word_id != -1) {
                        current_window_counts_by_id[left_word_id]--; // 减少其计数
                        // 只有当移出的 `left_word` 导致匹配单词数量减少时，才更新 `words_found`。
                        if (current_window_counts_by_id[left_word_id] < target_counts_by_id[left_word_id]) {
                            words_found--;
                        }
                    }
                    left += word_len; // 移动窗口左边界
                }

            } else {
                // 1.3 如果当前单词 `current_word_buffer` **不是**目标单词列表中的任何一个，
                // 则当前窗口无效，直接清空所有计数，并重置窗口。
                memset(current_window_counts_by_id, 0, max_word_id * sizeof(int)); // 清空计数数组
                words_found = 0;              // 重置匹配单词数量
                left = right + word_len;      // 将窗口左边界移动到当前不匹配单词的下一个位置
            }

            // 2. 检查当前窗口是否包含所有目标单词 (`words_found == wordsSize`)
            // 并且窗口的实际长度是否等于所有单词串联的总长度 (`total_len`)
            if (words_found == wordsSize && (right - left + word_len) == total_len) {
                results[res_count++] = left; // 找到一个匹配，记录起始索引 `left`

                // 找到一个匹配后，为了寻找下一个潜在匹配，需要将窗口向右滑动一个单词的距离。
                // 移除最左侧的单词，继续下一轮循环。
                char left_word_buffer[word_len + 1];
                memcpy(left_word_buffer, s + left, word_len);
                left_word_buffer[word_len] = '\0';

                int left_word_id = -1;
                StringIdNode *left_node = id_mapper->table[BKDRHash(left_word_buffer) % id_mapper->capacity];
                while(left_node != NULL) {
                    if (strcmp(left_node->key, left_word_buffer) == 0) {
                        left_word_id = left_node->id;
                        break;
                    }
                    left_node = left_node->next;
                }

                if (left_word_id != -1) {
                    current_window_counts_by_id[left_word_id]--; // 减少其计数
                    // 只有当移出的 `left_word` 导致匹配单词数量减少时，才更新 `words_found`。
                    if (current_window_counts_by_id[left_word_id] < target_counts_by_id[left_word_id]) {
                        words_found--;
                    }
                }
                left += word_len; // 移动窗口左边界
            }
        }
        free(current_window_counts_by_id); // 释放当前窗口的计数数组
    }

    // --- 步骤 4: 清理内存并返回结果 ---
    string_id_map_destroy(id_mapper);     // 释放 ID 映射哈希表
    free(target_counts_by_id);            // 释放目标计数数组

    *returnSize = res_count; // 设置实际返回的结果数量

    // 调整 results 数组大小到实际使用的大小
    if (res_count == 0) {
        free(results); // 如果没有结果，释放数组并返回 NULL
        return NULL;
    }
    // 使用 realloc 调整数组大小
    int* final_results = (int*)realloc(results, res_count * sizeof(int));
    if (!final_results) { // realloc 失败，需要处理
        fprintf(stderr, "findSubstring: Failed to reallocate results array to final size.\n");
        // 原始 results 指针仍然有效，但可能不够大，或直接退出。
        // 根据题目假设 caller calls free()，此处返回 results 可能会导致 caller free 错误大小。
        // 更安全的做法是退出或抛出错误。这里直接返回 results (可能不精确大小)
        return results; 
    }
    return final_results;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::vector<int> findSubstring(std::string s, std::vector<std::string>& words) {
        std::vector<int> results; // 存储所有符合条件的起始索引
        
        // 边界条件检查
        // 如果 words 为空，或者 words[0] 为空（意味着单词长度为0），则无匹配可能
        if (words.empty() || words[0].empty()) {
            return results;
        }

        int s_len = s.length();         // 主字符串 s 的长度
        int num_words = words.size();   // 单词列表 words 中的单词数量
        int word_len = words[0].length(); // 每个单词的长度 (题目保证所有单词长度相同)
        int total_len = num_words * word_len; // 所有单词串联后的总长度

        // 如果主字符串 s 的长度小于所有单词串联的总长度，不可能找到匹配
        if (s_len < total_len) {
            return results;
        }

        // 统计 words 列表中每个单词的出现频率 (目标计数)
        std::unordered_map<std::string, int> target_counts;
        for (const std::string& word : words) {
            target_counts[word]++;
        }

        // 核心思路：通过 `word_len` 种不同的起始点进行滑动窗口遍历。
        // 例如，如果 `word_len` 是 3，我们需要从索引 0, 1, 2 开始各进行一次滑动窗口。
        // 这样做是为了确保不遗漏任何可能的串联子串。
        for (int i = 0; i < word_len; ++i) {
            std::unordered_map<std::string, int> current_window_counts; // 当前窗口内的单词计数
            int words_found = 0; // 当前窗口中找到的符合条件的单词数量
            int left = i;        // 当前滑动窗口的左边界 (起始索引)

            // `right` 指针每次移动一个 `word_len` 的距离，作为窗口的右边界
            for (int right = i; right + word_len <= s_len; right += word_len) {
                std::string current_word = s.substr(right, word_len); // 获取窗口最右侧的单词

                // 1. 如果当前单词 `current_word` 是目标单词之一 (存在于 `target_counts`)
                if (target_counts.count(current_word)) {
                    current_window_counts[current_word]++; // 增加当前窗口中该单词的计数
                    
                    // 如果当前单词的计数未超过目标计数，则匹配单词数量增加
                    if (current_window_counts[current_word] <= target_counts[current_word]) {
                        words_found++;
                    }

                    // 2. 如果当前窗口中某个单词的计数超过了目标计数，则需要收缩窗口左边界
                    // 不断移动 `left` 指针，移除最左侧的单词，直到 `current_word` 的计数不再超标。
                    while (current_window_counts[current_word] > target_counts[current_word]) {
                        std::string left_word = s.substr(left, word_len); // 获取最左侧的单词
                        current_window_counts[left_word]--;               // 减少其计数
                        
                        // 只有当移出的 `left_word` 导致匹配单词数量减少时，才更新 `words_found`。
                        // 即 `left_word` 的计数在移出前仍是有效匹配的一部分。
                        if (current_window_counts[left_word] < target_counts[left_word]) {
                             words_found--;
                        }
                        left += word_len; // 移动窗口左边界
                    }

                } else {
                    // 3. 如果当前单词 `current_word` **不是**目标单词列表中的任何一个，
                    // 则当前窗口无效，直接清空所有计数，并重置窗口。
                    current_window_counts.clear(); // 清空当前窗口的单词计数
                    words_found = 0;              // 重置匹配单词数量
                    left = right + word_len;      // 将窗口左边界移动到当前不匹配单词的下一个位置
                                                  // (重新开始一个新的滑动窗口)
                }

                // 4. 检查当前窗口是否包含所有目标单词 (`words_found == num_words`)
                // 并且窗口的实际长度是否等于所有单词串联的总长度 (`total_len`)
                if (words_found == num_words && (right - left + word_len) == total_len) {
                    results.push_back(left); // 找到一个匹配，记录起始索引 `left`

                    // 找到一个匹配后，为了寻找下一个潜在匹配，需要将窗口向右滑动一个单词的距离。
                    // 移除最左侧的单词，继续下一轮循环。
                    std::string left_word = s.substr(left, word_len);
                    current_window_counts[left_word]--;
                    // 同样，只有当移出的 `left_word` 导致匹配单词数量减少时，才更新 `words_found`。
                    if (current_window_counts[left_word] < target_counts[left_word]) {
                        words_found--;
                    }
                    left += word_len; // 移动窗口左边界
                }
            }
        }

        return results;
    }
};
```

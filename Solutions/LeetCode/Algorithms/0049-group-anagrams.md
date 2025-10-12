# Question

[group-anagrams](https://leetcode.cn/problems/group-anagrams/)



# Answer

> solution

解决“字母异位词分组”问题的最佳实践是：**选择一种高效的字符串“规范化”方法来生成哈希键，然后将原始字符串存储到以该键为索引的哈希表中。**

1.  **第一步永远是简化数据结构。**
    *   **思想：** 哈希表是核心，它提供了 `O(1)` 平均时间复杂度的查找、插入和删除。
    *   **实现：**
        *   C++ 使用 `std::unordered_map<std::string, std::vector<std::string>>`。键是规范化字符串，值是原始字符串的列表。
        *   C 语言需要手动实现哈希表（通常采用分离链表法），并通过 `struct` 来组织键和值。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   **空字符串数组：** 如果输入 `strs` 是空的，直接返回一个空的列表。
    *   **字符串为空：** 空字符串本身也是异位词，可以作为分组。
    *   **通用性：** 规范化键的方法应适用于所有字符串，无论其内容如何。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **核心：键生成策略**
        有两种主要的有效方法来生成规范化键：
        *   **a. 排序字符串法：**
            *   **思想：** 对每个字符串进行字母排序。例如 "eat" -> "aet"，"tea" -> "aet"。排序后的字符串就是键。
            *   **优点：** 简单直观。
            *   **缺点：** 每次排序的时间复杂度为 `O(K log K)`（K 为字符串平均长度），总时间复杂度 `O(N * K log K)`。
        *   **b. 字符计数法：** （这是我们最终在 C++ 和 C 方案中选择的优化方法）
            *   **思想：** 统计每个字符串中，26 个小写字母（a-z）出现的频率。然后将这些频率转换成一个唯一的字符串作为键。例如 "eat" -> "a1e1t1" 或 "a1#e1#t1"。
            *   **优点：** 统计频率的时间复杂度为 `O(K)`，键字符串构建的时间复杂度也为 `O(K)`（或 `O(1)` 如果键是固定长度的数组）。总时间复杂度 `O(N * K)`，通常比排序法更快。
            *   **实现细节（C++ / C）：**
                *   使用一个 `int counts[26]` 数组来统计频率。
                *   构建键字符串时，遍历 `counts` 数组。对于每个字母 `char c = 'a' + i`，将其和它的频率 `counts[i]` 拼接起来。例如 `key_builder.push_back(c); key_builder += std::to_string(counts[i]);` (C++) 或 `sprintf` / `append_int_to_key` (C)。
    *   **哈希表的填充：**
        *   对于 `strs` 中的每个原始字符串 `str`：
            1.  生成其规范化键 `key`。
            2.  将 `str` 添加到 `hash_table[key]` 对应的 `std::vector<std::string>` (C++) 或 `AnagramNode` (C) 中。
    *   **结果收集：**
        *   遍历哈希表，将所有 `value` 部分（即各个异位词组的列表）收集起来，形成最终的 `vector<vector<string>>` (C++) 或 `char*** lists` (C)。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   所有方案都只读取原始输入 `strs` 数组，不会对其进行修改。
    *   **C 语言的内存管理：** 这是 C 语言版本的最大挑战。必须手动 `malloc` 哈希表的结构体、链表节点、键字符串、索引数组、最终结果 `char*** lists` 和 `int** returnColumnSizes`，并且**必须**在函数返回前释放所有临时分配的内存（例如临时键字符串、哈希表结构本身）。调用者必须 `free` 返回的 `lists` 和 `returnColumnSizes`，以及 `lists` 中的每个子 `char**`。这是 C 语言接口的规范。
    *   **C++ 的内存管理：** `std::unordered_map` 和 `std::vector<std::string>` 容器负责自动管理内存，使得代码简洁且不易出错。

**总结来说，解决“字母异位词分组”的核心是为异位词找到一个稳定的、唯一的标识符（键），并通过哈希表进行高效分组。字符计数法通常比排序法更高效，并且 C++ 的 STL 提供了极其方便的哈希表实现，而 C 语言则需要更精细的手动内存管理和哈希表实现。**

### Way Of C

> source code

```c
// 定义一个链表节点，用于处理哈希冲突和存储异位词索引
typedef struct AnagramNode {
    char* sorted_key;        // 规范化后的字符串键
    int* original_indices;   // 存储原始字符串在 strs 数组中的索引
    int count;               // 当前链表节点存储的异位词数量
    int capacity;            // original_indices 数组的容量
    struct AnagramNode* next; // 链表指针，处理冲突
} AnagramNode;

// 定义哈希表结构
typedef struct {
    AnagramNode** buckets;   // 哈希桶数组
    int size;                // 哈希表大小
    int num_groups;          // 实际的异位词组数量
} AnagramHashTable;

// 初始化哈希表
static AnagramHashTable* createHashTable(int initialSize) {
    AnagramHashTable* ht = (AnagramHashTable*)malloc(sizeof(AnagramHashTable));
    if (ht == NULL) { fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }
    ht->size = initialSize;
    ht->num_groups = 0;
    ht->buckets = (AnagramNode**)calloc(initialSize, sizeof(AnagramNode*)); // calloc 初始化为 NULL
    if (ht->buckets == NULL) { free(ht); fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }
    return ht;
}

// BKDRHash 函数（或替换为其他更优哈希函数）
static unsigned int BKDRHash(const char *s, int htSize) {
    unsigned int hash = 0;
    unsigned int seed = 131; // 更好的种子
    while (*s != '\0') {
        hash = hash * seed + (*s++);
    }
    return hash % htSize;
}

// 插入或查找哈希表中的键
static AnagramNode* findOrCreateNode(AnagramHashTable* ht, const char* key) {
    unsigned int hash_val = BKDRHash(key, ht->size);
    AnagramNode* current = ht->buckets[hash_val];

    // 线性查找链表，处理冲突
    while (current != NULL) {
        if (strcmp(current->sorted_key, key) == 0) {
            return current; // 找到匹配的键
        }
        current = current->next;
    }

    // 未找到，创建新节点
    AnagramNode* newNode = (AnagramNode*)malloc(sizeof(AnagramNode));
    if (newNode == NULL) { fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }
    
    newNode->sorted_key = (char*)malloc(strlen(key) + 1);
    if (newNode->sorted_key == NULL) { free(newNode); fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }
    strcpy(newNode->sorted_key, key);
    
    newNode->original_indices = (int*)malloc(10 * sizeof(int)); // 初始容量10
    if (newNode->original_indices == NULL) { free(newNode->sorted_key); free(newNode); fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }
    newNode->capacity = 10;
    newNode->count = 0;
    
    // 将新节点添加到链表头部
    newNode->next = ht->buckets[hash_val];
    ht->buckets[hash_val] = newNode;
    ht->num_groups++; // 新增一个异位词组
    
    return newNode;
}

// 释放哈希表内存
static void freeHashTable(AnagramHashTable* ht) {
    for (int i = 0; i < ht->size; ++i) {
        AnagramNode* current = ht->buckets[i];
        while (current != NULL) {
            AnagramNode* next = current->next;
            free(current->sorted_key);
            free(current->original_indices);
            free(current);
            current = next;
        }
    }
    free(ht->buckets);
    free(ht);
}

// 将整数转换为字符串，追加到 key_builder
// C 语言中没有 std::to_string，需要手动实现或使用 sprintf
static void append_int_to_key(char** key_builder_ptr, int* current_key_len_ptr, int* current_key_capacity_ptr, int val) {
    // 假设 val 不会非常大，10 位数足以
    char num_str[12]; // 10 digits + sign + null terminator
    sprintf(num_str, "%d", val);
    int num_str_len = strlen(num_str);

    // 检查容量，如果不够就扩容
    if (*current_key_len_ptr + num_str_len + 1 > *current_key_capacity_ptr) {
        *current_key_capacity_ptr *= 2; // 双倍扩容
        *key_builder_ptr = (char*)realloc(*key_builder_ptr, *current_key_capacity_ptr);
        if (*key_builder_ptr == NULL) { fprintf(stderr, "realloc failed\n"); exit(EXIT_FAILURE); }
    }
    strcat(*key_builder_ptr, num_str);
    *current_key_len_ptr += num_str_len;
}

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *returnColumnSizes array must be malloced, assume caller calls free().
 */
char*** groupAnagrams(char** strs, int strsSize, int* returnSize, int** returnColumnSizes) {
    // 处理空输入
    if (strsSize == 0) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    // 1. 初始化哈希表 (初始大小可以根据 strsSize 调整，例如 2 * strsSize)
    // 选择一个素数作为哈希表大小可以减少冲突，这里简单地用 2 * strsSize
    int initial_hash_table_size = strsSize > 0 ? strsSize * 2 : 11; // 最小为11
    AnagramHashTable* ht = createHashTable(initial_hash_table_size);

    // 2. 遍历输入字符串，为每个字符串生成键并插入哈希表
    for (int i = 0; i < strsSize; ++i) {
        // 字符计数法生成键
        int counts[26] = { 0 };
        for (int k = 0; strs[i][k] != '\0'; ++k) {
            counts[strs[i][k] - 'a']++;
        }

        // 构建键字符串 (例如 "a1b0c2d0..." )
        // 动态构建键字符串，预分配一些空间
        int current_key_capacity = 26 * 2 + 1; // 26 chars + 26 digits + null terminator
        char* key_builder = (char*)malloc(current_key_capacity);
        if (key_builder == NULL) { freeHashTable(ht); fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }
        key_builder[0] = '\0'; // 初始化为空字符串
        int current_key_len = 0;

        for (int k = 0; k < 26; ++k) {
            // 先追加字符 'a' + k
            char char_to_append[2] = { (char)('a' + k), '\0' };
            append_int_to_key(&key_builder, &current_key_len, &current_key_capacity, char_to_append[0]);
            
            // 再追加频率
            append_int_to_key(&key_builder, &current_key_len, &current_key_capacity, counts[k]);
        }

        // 查找或创建哈希表节点
        AnagramNode* node = findOrCreateNode(ht, key_builder);
        
        // 确保 original_indices 数组容量足够
        if (node->count >= node->capacity) {
            node->capacity *= 2; // 双倍扩容
            node->original_indices = (int*)realloc(node->original_indices, node->capacity * sizeof(int));
            if (node->original_indices == NULL) { free(key_builder); freeHashTable(ht); fprintf(stderr, "realloc failed\n"); exit(EXIT_FAILURE); }
        }
        node->original_indices[node->count++] = i; // 存储原始索引

        free(key_builder); // 释放临时键字符串
    }

    // 3. 收集结果
    *returnSize = ht->num_groups;
    char*** lists = (char***)malloc(ht->num_groups * sizeof(char**));
    if (lists == NULL) { freeHashTable(ht); fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }
    
    *returnColumnSizes = (int*)malloc(ht->num_groups * sizeof(int));
    if (*returnColumnSizes == NULL) { free(lists); freeHashTable(ht); fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE); }

    int current_group_idx = 0;
    for (int i = 0; i < ht->size; ++i) {
        AnagramNode* current = ht->buckets[i];
        while (current != NULL) {
            (*returnColumnSizes)[current_group_idx] = current->count;
            lists[current_group_idx] = (char**)malloc(current->count * sizeof(char*));
            if (lists[current_group_idx] == NULL) {
                // 内存分配失败，需要释放前面已分配的 lists[k]
                for(int k=0; k<current_group_idx; ++k) free(lists[k]);
                free(lists); free(*returnColumnSizes); freeHashTable(ht);
                fprintf(stderr, "malloc failed\n"); exit(EXIT_FAILURE);
            }
            for (int j = 0; j < current->count; ++j) {
                lists[current_group_idx][j] = strs[current->original_indices[j]]; // 引用原始字符串
            }
            current_group_idx++;
            current = current->next;
        }
    }

    freeHashTable(ht); // 释放哈希表本身
    
    return lists;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::vector<std::vector<std::string>> groupAnagrams(std::vector<std::string>& strs) {
        std::unordered_map<std::string, std::vector<std::string>> hash_table;

        // 遍历输入字符串列表
        for (const auto& str : strs) { // 使用 const 引用，避免不必要的字符串拷贝
            // 字符计数法生成键：O(K) 时间复杂度
            int counts[26] = { 0 }; // 固定大小数组，用于统计 'a' 到 'z' 的频率
            for (char c : str) {    // 遍历当前字符串的每个字符
                counts[c - 'a']++;  // 统计频率
            }

            // 构建规范化键字符串
            // 格式示例: "#1#0#2#0...#" (a出现1次, b出现0次, c出现2次...)
            std::string key_builder; 
            for (int i = 0; i < 26; ++i) { // 遍历所有 26 个字母的频率
                key_builder.push_back((char)('a' + i)); // 添加字符本身
                key_builder += std::to_string(counts[i]); // 将频率转换为字符串并追加
                // 例如: "a1b0c2"
                // 这样做比 '#1#0#2' 更简洁，并且不会因为频率是多位数而错误。
            }

            // 将原始字符串添加到对应键的列表中
            hash_table[key_builder].push_back(str); 
        }

        // 遍历哈希表，将所有值（即异位词列表）收集到结果集中
        std::vector<std::vector<std::string>> result;
        // 使用 const auto& 避免拷贝
        for (const auto& pair : hash_table) {
            result.push_back(pair.second);
        }

        return result;
    }
};
```

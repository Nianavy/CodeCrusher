# Question

[group-anagrams](https://leetcode.cn/problems/group-anagrams/)



# Answer

> solution

**通过对每个字符串进行字母排序来生成其唯一的规范化键，然后利用哈希表将具有相同键的原始字符串收集在一起。**

#### **通用解决思路步骤：**

1.  **处理特殊情况：**
    *   如果输入字符串数组为空 (`strsSize == 0` 或 `strs.empty()`)，则返回一个空的结果集。

2.  **遍历并生成键：**
    *   对于 `strs` 中的每一个原始字符串 `S`：
        *   创建一个 `S` 的副本。
        *   对这个副本进行字母排序。例如，`"eat"` 排序后变成 `"aet"`，`"tea"` 排序后也变成 `"aet"`。
        *   这个排序后的字符串 `Key_S` 就是该异位词组的唯一键。
    *   **时间成本：** 对于每个长度为 `K` 的字符串，排序需要 `O(K log K)`。如果有 `N` 个字符串，这一步的总时间是 `O(N * K log K)`。

3.  **哈希表分组：**
    *   初始化一个哈希表。
    *   使用 `Key_S` 作为哈希表的键，将原始字符串 `S` 添加到 `hash_table[Key_S]` 对应的列表中。
    *   如果 `Key_S` 是第一次出现，哈希表会自动为它创建一个新的列表。
    *   **时间成本：** 哈希表的平均插入和查找操作为 `O(1)` (不计键生成时间)。

4.  **收集结果：**
    *   遍历哈希表，将所有键对应的列表（即异位词组）收集起来，形成最终的二维结果集。

#### **C++ 实现 ：**

*   **实现特点：**
    *   利用 C++ STL 的强大功能：`std::string`、`std::vector` 和 `std::unordered_map`。
    *   `std::string nstr = str;` 方便地创建副本。
    *   `std::sort(nstr.begin(), nstr.end());` 对字符串副本进行排序。
    *   `hash[nstr].push_back(str);` 简洁地将原始字符串添加到哈希表中相应键的值列表中。
    *   自动处理内存管理、哈希冲突和容器扩容。
*   **Linus 的评价：** **🟢 好品味。** 这是 C++ 处理此类问题的典范，代码清晰、简洁、高效且安全。

#### **C 语言实现：**

*   **实现特点：**
    *   **手动哈希表：** 手动定义 `AnagramGroup_C` 结构体作为哈希表的槽位，并实现哈希函数 (`BKDRHash_c`)。
    *   **冲突解决：** 采用**开放寻址法**（具体是线性探测）处理哈希冲突。当哈希到已占用槽位时，会探测下一个位置。
    *   **动态数组：** 每个哈希槽内部的 `original_indices` 数组是动态分配的，需要手动 `malloc` 和 `realloc` 进行扩容。
    *   **字符串管理：** 排序键需要手动 `malloc` 副本 (`sorted_strs_keys`)，并在哈希表中存储键时再次 `malloc` 拷贝。
    *   **严格的内存管理：** 代码中包含大量的 `malloc`、`free` 调用和 `NULL` 检查，确保程序在内存不足或结束时能够正确地分配和释放所有资源，避免内存泄漏和崩溃。这是 C 语言实现的复杂性和挑战所在。
*   **Linus 的评价：** 这个版本虽然因手动内存管理而显得冗长，但它是一个**健壮且正确的 C 语言实现**。它展示了在没有高级语言特性下，如何系统地构建哈希表和管理内存。

**最终总结：**
两种语言的实现都殊途同归地应用了**“排序字符串为键 + 哈希表分组”**的核心算法。C++ 凭借其高级抽象和强大的标准库，使得这一过程变得极其简洁和安全；而 C 语言则通过底层的内存控制和手动数据结构实现，展示了在没有这些抽象的情况下，如何系统而健壮地解决问题。C 语言的实现虽然复杂，但它符合 C 语言直接操作内存的哲学，并且在正确实现的前提下，能够提供可控的性能。

### Way Of C

> source code

```c
// 哈希表节点结构体
typedef struct {
    char *sorted_key;       // 排序后的字符串作为键
    int *original_indices;  // 动态数组，存储原始字符串在 strs 数组中的索引
    int count;              // 当前异位词组中的字符串数量
    int capacity;           // original_indices 数组的当前容量
} AnagramGroup_C;

// 比较函数，用于 qsort 排序字符串中的字符
static int compare_chars_c(const void *a, const void *b)
{
    return *(const char *) a - *(const char *) b;
}

// BKDRHash 函数（通用哈希函数，用于 char* 键）
static unsigned int BKDRHash_c(const char *s, int htSize)
{
    unsigned int hash = 0;
    unsigned int seed = 131; // 经验证较好的种子
    while (*s != '\0') {
        hash = hash * seed + (*s++);
    }
    return hash % htSize;
}

// 释放单个 AnagramGroup_C 节点内部的内存
static void freeAnagramGroup_C(AnagramGroup_C* group) {
    if (group->sorted_key) {
        free(group->sorted_key);
    }
    if (group->original_indices) {
        free(group->original_indices);
    }
}

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *returnColumnSizes array must be malloced, assume caller calls free().
 */
char*** groupAnagrams(char** strs, int strsSize, int* returnSize, int** returnColumnSizes)
{
    // 1. 处理空输入
    if (strsSize == 0) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    // 2. 初始化哈希表
    // 采用开放寻址法（线性探测）。哈希表大小选择一个比 strsSize 大的素数，减少冲突。
    // 这里选择一个相对较大的素数作为初始容量，以降低冲突率。
    // 如果 strsSize 非常大，可能需要实现动态扩容。
    int hash_table_capacity = strsSize * 2; // 至少是 strsSize 的两倍
    if (hash_table_capacity < 101) hash_table_capacity = 101; // 确保最小容量
    
    AnagramGroup_C* ht = (AnagramGroup_C*)calloc(hash_table_capacity, sizeof(AnagramGroup_C)); // calloc 初始化为 0
    if (ht == NULL) { fprintf(stderr, "malloc failed for hash table\n"); exit(EXIT_FAILURE); }

    // 3. 遍历输入字符串，生成排序键，并填充哈希表
    // sorted_strs_keys 数组用于存储每个 strs[i] 的副本，并在副本上排序，避免修改原始字符串
    // 并在后面用于释放临时分配的排序键内存。
    char **sorted_strs_keys = (char**)malloc(strsSize * sizeof(char *));
    if (sorted_strs_keys == NULL) { free(ht); fprintf(stderr, "malloc failed for sorted_strs_keys array\n"); exit(EXIT_FAILURE); }

    int num_unique_groups = 0; // 记录最终异位词组的数量

    for (int i = 0; i < strsSize; i++) {
        int len = strlen(strs[i]);
        sorted_strs_keys[i] = (char*)malloc(len + 1); // 为当前字符串的排序副本分配内存
        if (sorted_strs_keys[i] == NULL) { 
            // 内存分配失败，释放之前分配的所有 sorted_strs_keys 和 ht
            for(int k=0; k<i; ++k) free(sorted_strs_keys[k]);
            free(sorted_strs_keys);
            for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]); // 释放已初始化的哈希组
            free(ht);
            fprintf(stderr, "malloc failed for sorted_strs_keys[%d]\n", i); 
            exit(EXIT_FAILURE); 
        }
        strcpy(sorted_strs_keys[i], strs[i]);
        qsort(sorted_strs_keys[i], len, sizeof(char), compare_chars_c); // 排序字符串作为键

        unsigned int hash_val = BKDRHash_c(sorted_strs_keys[i], hash_table_capacity);
        int current_pos = hash_val;
        bool found = false;

        // 线性探测处理哈希冲突
        while (ht[current_pos].sorted_key != NULL) {
            if (strcmp(ht[current_pos].sorted_key, sorted_strs_keys[i]) == 0) {
                found = true;
                break; // 找到匹配的键
            }
            current_pos = (current_pos + 1) % hash_table_capacity; // 探测下一个位置
            if (current_pos == hash_val) { // 绕了一圈，哈希表已满或无法找到
                // 这意味着哈希表太小，无法处理所有元素。在实际应用中，这里应该触发哈希表扩容。
                // 为了简化，这里直接退出，表示哈希表设计有问题。
                fprintf(stderr, "Hash table full or unable to find key, capacity too small.\n");
                for(int k=0; k<=i; ++k) if(sorted_strs_keys[k]) free(sorted_strs_keys[k]); free(sorted_strs_keys);
                for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]);
                free(ht);
                exit(EXIT_FAILURE);
            }
        }
        
        // 如果是新组，初始化其结构
        if (!found) {
            // 拷贝排序键到哈希表中的 AnagramGroup_C 结构
            ht[current_pos].sorted_key = (char*)malloc(len + 1); 
            if (ht[current_pos].sorted_key == NULL) { 
                for(int k=0; k<=i; ++k) if(sorted_strs_keys[k]) free(sorted_strs_keys[k]); free(sorted_strs_keys);
                for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]);
                free(ht);
                fprintf(stderr, "malloc failed for ht[current_pos].sorted_key\n"); exit(EXIT_FAILURE);
            }
            strcpy(ht[current_pos].sorted_key, sorted_strs_keys[i]);
            
            ht[current_pos].capacity = 4; // original_indices 初始容量
            ht[current_pos].original_indices = (int*)malloc(ht[current_pos].capacity * sizeof(int));
            if (ht[current_pos].original_indices == NULL) { 
                free(ht[current_pos].sorted_key);
                for(int k=0; k<=i; ++k) if(sorted_strs_keys[k]) free(sorted_strs_keys[k]); free(sorted_strs_keys);
                for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]);
                free(ht);
                fprintf(stderr, "malloc failed for ht[current_pos].original_indices\n"); exit(EXIT_FAILURE);
            }
            ht[current_pos].count = 0;
            num_unique_groups++; // 新增一个异位词组
        }
        
        // 确保 original_indices 数组容量足够，并存储原始索引
        if (ht[current_pos].count == ht[current_pos].capacity) {
            ht[current_pos].capacity *= 2; // 双倍扩容
            ht[current_pos].original_indices = (int*)realloc(ht[current_pos].original_indices, ht[current_pos].capacity * sizeof(int));
            if (ht[current_pos].original_indices == NULL) { 
                // realloc 失败，释放所有资源
                for(int k=0; k<=i; ++k) if(sorted_strs_keys[k]) free(sorted_strs_keys[k]); free(sorted_strs_keys);
                for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]);
                free(ht);
                fprintf(stderr, "realloc failed for original_indices\n"); exit(EXIT_FAILURE); 
            }
        }
        ht[current_pos].original_indices[ht[current_pos].count++] = i; // 存储原始字符串索引
    }

    // 4. 收集结果
    *returnSize = num_unique_groups;
    char*** lists = (char***)malloc(num_unique_groups * sizeof(char**));
    if (lists == NULL) { 
        // 内存分配失败，释放所有资源
        for(int k=0; k<strsSize; ++k) if(sorted_strs_keys[k]) free(sorted_strs_keys[k]); free(sorted_strs_keys);
        for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]); free(ht);
        fprintf(stderr, "malloc failed for lists\n"); exit(EXIT_FAILURE); 
    }
    
    *returnColumnSizes = (int*)malloc(num_unique_groups * sizeof(int));
    if (*returnColumnSizes == NULL) { 
        // 内存分配失败，释放所有资源
        free(lists);
        for(int k=0; k<strsSize; ++k) if(sorted_strs_keys[k]) free(sorted_strs_keys[k]); free(sorted_strs_keys);
        for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]); free(ht);
        fprintf(stderr, "malloc failed for returnColumnSizes\n"); exit(EXIT_FAILURE); 
    }

    int current_group_idx = 0;
    for (int i = 0; i < hash_table_capacity; ++i) {
        if (ht[i].sorted_key != NULL) { // 找到一个有效组
            (*returnColumnSizes)[current_group_idx] = ht[i].count;
            lists[current_group_idx] = (char**)malloc(ht[i].count * sizeof(char*));
            if (lists[current_group_idx] == NULL) {
                // 内存分配失败，释放前面已分配的 lists[k]
                for(int k=0; k<current_group_idx; ++k) free(lists[k]);
                free(lists); free(*returnColumnSizes); 
                for(int k=0; k<strsSize; ++k) if(sorted_strs_keys[k]) free(sorted_strs_keys[k]); free(sorted_strs_keys);
                for(int k=0; k<hash_table_capacity; ++k) freeAnagramGroup_C(&ht[k]); free(ht);
                fprintf(stderr, "malloc failed for lists[%d]\n", current_group_idx); exit(EXIT_FAILURE);
            }
            for (int j = 0; j < ht[i].count; ++j) {
                lists[current_group_idx][j] = strs[ht[i].original_indices[j]]; // 引用原始字符串
            }
            current_group_idx++;
        }
    }

    // 5. 释放所有辅助内存
    for(int i = 0; i < strsSize; ++i) { // 释放 sorted_strs_keys 数组中的每个字符串
        if(sorted_strs_keys[i]) free(sorted_strs_keys[i]);
    }
    free(sorted_strs_keys); // 释放 sorted_strs_keys 数组本身

    for (int i = 0; i < hash_table_capacity; ++i) { // 释放哈希表中的每个键和索引数组
        freeAnagramGroup_C(&ht[i]); // 使用辅助函数释放
    }
    free(ht); // 释放哈希表本身
    
    return lists;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> hash; // 使用 C++ STL 的 unordered_map

        // 遍历输入字符串列表
        for (auto& str: strs) {
            string nstr = str; // 复制原始字符串
            sort(nstr.begin(), nstr.end()); // 对副本进行排序，生成规范化键
            hash[nstr].push_back(str); // 将原始字符串添加到对应键的列表中
        }

        vector<vector<string>> res;
        // 遍历哈希表，将所有值（即异位词列表）收集到结果集中
        for (auto& item : hash) res.push_back(item.second);

        return res; // 返回结果
    }
};
```

# Question

[fraction-to-recurring-decimal](https://leetcode.cn/problems/fraction-to-recurring-decimal/)



# Answer

> solution

### **C++ 版本**

*   **核心算法**：整数部分直接计算。小数部分通过连续 `x *= 10, x %= y` 循环，使用 `std::unordered_map` 存储余数及其在结果字符串中的位置，从而检测循环节。
*   **关键点**：`long long` 避免溢出。直接拼接字符串。
*   **优点**：代码简洁，利用了标准库，逻辑清晰。
*   **缺点**：**未处理 `denominator == 0`**（致命缺陷）。对于极长的循环节，`unordered_map` 的开销可能较大。

### **C 版本**

*   **核心算法**：数学逻辑与 C++ 相同。
*   **关键点**：
    1.  **手动内存管理**：使用 `malloc`/`realloc` 构建 `char*` 字符串。
    2.  **专用哈希表**：在 C 语言中实现一个简单的链地址法哈希表来替代 `unordered_map`，避免内存爆炸（如分配 `denominator` 大小的数组），空间复杂度与循环节长度成正比。
    3.  **健壮性**：必须检查 `denominator == 0` 和内存分配失败。
    4.  **INT_MIN 安全**：使用 `long long` 运算。
*   **优点**：内存安全，健壮，可移植。
*   **缺点**：代码复杂，需要实现基础数据结构，易出错。

### **核心区别**

*   **复杂性**：C++ 版利用语言特性（STL、自动内存）专注于算法；C 版必须手动处理内存和数据结构，复杂性增加了，但这是 C 语言实现健壮性和可移植性所必需的代价。

### Way Of C

> source code

```c
// 简单的哈希表节点，用于存储 (余数，位置) 对
typedef struct HashNode {
    int remainder;
    int position;
    struct HashNode* next;
} HashNode;
// 简单的哈希表结构
typedef struct {
    HashNode** buckets; // 桶数组
    int size;           // 桶的数量
} SimpleHashTable;
// 哈希函数（简单的余数模桶数）
static int hash_func(int key, int size) {
    // 注意：key 可能是负数，需要取绝对值再模，或者处理符号
    // 这里 key 是余数，在计算中被转换为了正数
    return key % size;
}
// 初始化哈希表
static SimpleHashTable* create_hash_table(int size) {
    SimpleHashTable* table = malloc(sizeof(SimpleHashTable));
    if (!table) return NULL;
    table->size = size;
    table->buckets = calloc(size, sizeof(HashNode*));
    if (!table->buckets) {
        free(table);
        return NULL;
    }
    return table;
}
// 插入元素
static void hash_insert(SimpleHashTable* table, int remainder, int position) {
    int bucket_idx = hash_func(remainder, table->size);
    HashNode* node = malloc(sizeof(HashNode));
    if (!node) return; // 内存分配失败，忽略，可能导致无法检测重复
    node->remainder = remainder;
    node->position = position;
    node->next = table->buckets[bucket_idx];
    table->buckets[bucket_idx] = node;
}
// 查找元素，返回位置，如果未找到返回 -1
static int hash_find(SimpleHashTable* table, int remainder) {
    int bucket_idx = hash_func(remainder, table->size);
    HashNode* current = table->buckets[bucket_idx];
    while (current) {
        if (current->remainder == remainder) {
            return current->position;
        }
        current = current->next;
    }
    return -1;
}
// 释放哈希表
static void free_hash_table(SimpleHashTable* table) {
    if (!table) return;
    for (int i = 0; i < table->size; i++) {
        HashNode* current = table->buckets[i];
        while (current) {
            HashNode* temp = current;
            current = current->next;
            free(temp);
        }
    }
    free(table->buckets);
    free(table);
}
/**
 * 将有理数转换为十进制字符串。
 * 
 * 注意：调用者需要负责释放返回的 char* 内存。
 * 
 * @param numerator   分子
 * @param denominator 分母
 * 
 * @return 动态分配的字符串（调用者需 free）。
 *         如果发生错误（除零、内存分配失败），返回 NULL。
 */
char* fractionToDecimal(int numerator, int denominator) {
    // 1. 防御性编程: 检查除零
    if (denominator == 0) {
        // printf("Error: Division by zero.\n"); // 现在不应该打印，因为这是API函数
        return NULL;
    }
    // 2. 处理整数情况 (0/n)
    if (numerator == 0) {
        char* one = (char*)malloc(2);
        if (one == NULL) return NULL;
        strcpy(one, "0");
        return one;
    }
    // 3. 处理符号
    int isNegative = 0;
    if ((numerator < 0) ^ (denominator < 0)) {
        isNegative = 1;
    }
    // 使用 long long 避免 INT_MIN 取绝对值溢出，并处理乘以10的溢出
    long long num = numerator;
    long long den = denominator;
    // 统一转换为正数进行计算
    if (num < 0) num = -num; 
    if (den < 0) den = -den;
    // --- 字符串构建部分 ---
    // 为了安全，我们使用动态构建。先计算整数部分和小数部分，再拼接。
    // 步骤 A: 生成整数部分
    long long integerPart = num / den;
    long long remainder = num % den;
    // 临时缓冲区用于数字转换
    char buffer[32]; 
    int bufferLen = 0;
    if (isNegative) {
        buffer[0] = '-';
        bufferLen = 1;
    }
    // 将整数部分转换为字符串
    char temp[32];
    int idx = 0;
    if (integerPart == 0) temp[idx++] = '0';
    else {
        if (integerPart < 0) { // 应该不会发生，因为 num 已经是正数
             integerPart = -integerPart;
        }
        while (integerPart > 0) {
            temp[idx++] = (integerPart % 10) + '0';
            integerPart /= 10;
        }
    }
    // 拷贝到 buffer
    if (isNegative && idx == 1 && temp[0] == '0') {
        // 如果整数部分是0且是负数，形式是 "-0"
        buffer[bufferLen++] = '0';
    } else {
        while (idx > 0) {
            buffer[bufferLen++] = temp[--idx];
        }
    }
    // 如果余数为0，整数部分就是结果
    if (remainder == 0) {
        char* result = (char*)malloc(bufferLen + 1);
        if (result == NULL) return NULL;
        memcpy(result, buffer, bufferLen);
        result[bufferLen] = '\0';
        return result;
    }
    // 步骤 B: 生成小数部分
    // 分配初始内存，稍后可能会 realloc
    size_t alloc_size = bufferLen + 16; // 预留一些空间
    char* result = (char*)malloc(alloc_size);
    if (result == NULL) {
        free_hash_table(NULL); // 如果有表需要清理
        return NULL;
    }
    // 复制整数部分和小数点
    memcpy(result, buffer, bufferLen);
    result[bufferLen] = '.';
    int res_len = bufferLen + 1;
    // 核心循环：检测循环节
    // 我们使用一个简单的哈希表来存储余数，避免分配巨大的内存。
    // 桶的数量选择一个合适的值，比如 10007。
    int hash_bit = 10007;
    SimpleHashTable* hash = create_hash_table(hash_bit);
    if (hash == NULL) {
        free(result);
        return NULL;
    }
    while (remainder != 0) {
        // 检查当前余数是否出现过
        int prev_pos = hash_find(hash, (int)remainder); // remainder 可能很大，但先截断为 int
        if (prev_pos != -1) {
            // 找到循环节！
            // 插入 '(' 在 prev_pos
            // 为了不移动整个字符串，我们可以重新构建，或者使用 memmove。
            // 这里使用 memmove，因为字符串不会太长（在合理测试用例下）。
            
            // 扩展缓冲区
            if (res_len + 3 >= alloc_size) {
                alloc_size = res_len + 10;
                char* new_result = (char*)realloc(result, alloc_size);
                if (new_result == NULL) {
                    free(result);
                    free_hash_table(hash);
                    return NULL;
                }
                result = new_result;
            }
            // 在循环节开始处插入 '('
            memmove(result + prev_pos + 1, result + prev_pos, res_len - prev_pos);
            result[prev_pos] = '(';
            // 在末尾追加 ')'
            result[res_len + 1] = ')';
            result[res_len + 2] = '\0';
            
            free_hash_table(hash);
            return result;
        }
        // 记录当前余数的位置（在结果字符串中的位置）
        hash_insert(hash, (int)remainder, res_len);
        // 计算下一位小数
        remainder *= 10;
        long long digit = remainder / den;
        remainder %= den; // 准备下一次循环
        // 扩展缓冲区
        if (res_len + 2 >= alloc_size) {
            alloc_size *= 2;
            char* new_result = (char*)realloc(result, alloc_size);
            if (new_result == NULL) {
                free(result);
                free_hash_table(hash);
                return NULL;
    }
            result = new_result;
        }
        // 追加小数位
        result[res_len++] = digit + '0';
    }
    // 如果余数为 0 退出循环（非循环节，是有限小数）
    result[res_len] = '\0';
    free_hash_table(hash);
    
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    string fractionToDecimal(int numerator, int denominator) {
        // 使用 int64_t 避免在计算 C++ INT_MIN * 10 时溢出
        typedef long long LL;
        LL x = numerator, y = denominator;
        // 特殊情况1: 分数是整数（余数为0）
        // 例如: 2/1 = "2", -3/1 = "-3"
        if (x % y == 0) return std::to_string(x / y);
        std::string res;
        // 特殊情况2: 处理符号
        // 如果分子和分母异号，结果为负数
        if ((x < 0) ^ (y < 0)) res += '-';
        // 转换为正数进行后续计算
        x = std::abs(x);
        y = std::abs(y);
        // 添加整数部分
        res += std::to_string(x / y) + '.';
        x %= y; // 更新为余数，用于小数部分计算
        // 哈希表用于检测循环节
        // key: 余数, value: 余数在结果字符串中的位置
        std::unordered_map<LL, int> hash;
        // 只要余数不为0，就继续计算小数部分
        while (x) {
            // 记录当前余数的位置
            hash[x] = res.size();
            // 计算下一位小数
            x *= 10;
            res += std::to_string(x / y);
            x %= y;
            // 检查当前余数是否出现过（形成循环节）
            if (hash.count(x)) {
                // 将循环节部分用括号包裹
                // hash[x] 是循环节开始的位置
                res = res.substr(0, hash[x]) + '(' + res.substr(hash[x]) + ')';
                break;
            }
        }
        return res;
    }
};
```

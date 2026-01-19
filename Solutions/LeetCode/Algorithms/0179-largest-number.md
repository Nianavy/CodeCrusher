# Question

[largest-number](https://leetcode.cn/problems/largest-number/)



# Answer

> solution

**解决思路**

1.  **核心算法**：比较两个数字 `a` 和 `b` 时，比较 `a+b` 与 `b+a` 的字符串字典序大小。如果 `a+b > b+a`，则 `a` 应排在 `b` 前面，以形成更大的拼接数字。
2.  **数据结构**：
    *   **C++**：将整数转为 `std::string`，放入 `vector<string>` 进行排序。使用引用避免不必要的复制。
    *   **C**：创建字符串指针数组 `char**`，每个指针指向转换后的整数字符串。排序后拼接并释放临时内存。
3.  **边界处理**：排序后，检查第一个字符串是否为 `"0"`。如果是，说明所有数字都是 `0`，直接返回 `"0"`。
4.  **内存管理**：C++ 版本利用标准库自动管理资源。C 版本需手动分配和释放临时字符串数组、指针数组及结果字符串，确保无内存泄漏。

### Way Of C

> source code

```c
// 比较函数：比较两个字符串指针的权重
int compare(const void *a, const void *b) {
    const char *sa = *(const char **)a;
    const char *sb = *(const char **)b;
    
    char buf1[64], buf2[64];
    snprintf(buf1, sizeof buf1, "%s%s", sa, sb);
    snprintf(buf2, sizeof buf2, "%s%s", sb, sa);
    
    // 如果 sa + sb > sb + sa, 则 sa 权重更大，sa 应该在前面。
    // 当 sb + sa > sa + sb 时，返回正值（sb 在 sa 前）。
    // 我们需要返回：sb + sa > sa + sb ? 1 : -1;
    // 更简单：return strcmp(buf2, buf1);
    return strcmp(buf2, buf1);
}
char* largestNumber(int* nums, int numsSize) {
    if (numsSize == 0) {
        return malloc(1) ? strcpy(malloc(1), "") : NULL;
    }
    // 分配字符串指针数组
    char **strs = malloc(numsSize * sizeof(char *));
    if (!strs) return NULL;
    // 转换整数到字符串
    for (int i = 0; i < numsSize; i++) {
        char temp[20];
        int len = snprintf(temp, sizeof temp, "%d", nums[i]);
        strs[i] = malloc(len + 1);
        if (!strs[i]) {
            for (int j = 0; j < i; j++) free(strs[j]);
            free(strs);
            return NULL;
        }
        strcpy(strs[i], temp);
    }
    // 排序
    qsort(strs, numsSize, sizeof(char *), compare);
    // 检查是否所有数字都是零
    if (strs[0][0] == '0') {
        char *result = malloc(2);
        if (result) {
            result[0] = '0';
            result[1] = '\0';
        }
        // 清理
        for (int i = 0; i < numsSize; i++) free(strs[i]);
        free(strs);
        return result;
    }
    // 计算总长度
    size_t total_len = 1;
    for (int i = 0; i < numsSize; i++) {
        total_len += strlen(strs[i]);
    }
    char *result = malloc(total_len);
    if (!result) {
        for (int i = 0; i < numsSize; i++) free(strs[i]);
        free(strs);
        return result;
    }
    result[0] = '\0';
    for (int i = 0; i < numsSize; i++) {
        strcat(result, strs[i]);
    }
    // 清理字符串指针
    for (int i = 0; i < numsSize; i++) free(strs[i]);
    free(strs);
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::string largestNumber(std::vector<int>& nums) {
        std::vector<std::string> strs;
        strs.reserve(nums.size());
        for (int num : nums) {
            strs.push_back(std::to_string(num));
        }
        // 排序：a 应排在 b 之前，当且仅当 a + b > b + a
        std::sort(strs.begin(), strs.end(), 
                  [](const std::string& a, const std::string& b) {
                      return a + b > b + a;
                  }
        );
        // 如果最大的数字是 "0"，则答案是 "0"
        if (!strs.empty() && strs[0] == "0") {
            return "0";
        }
        std::string result;
        // 预留空间，避免重复分配
        size_t total = 0;
        for (const auto& s : strs) {
            total += s.size();
        }
        result.reserve(total);
        for (const auto& s : strs) {
            result += s;
        }
        return result;
    }
};
```

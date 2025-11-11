# Question

[subsets](https://leetcode.cn/problems/subsets/)



# Answer

> solution

子集问题

### 1. 策略 I：位运算枚举 (最优简洁性)

当集合中的元素数量 $N$ 较小时（通常 $N \le 30$），这是最简洁、最快的方法。

*   **核心原理**：一个包含 $N$ 个元素的集合，有 $2^N$ 个子集。我们可以用 $0$ 到 $2^N - 1$ 的整数来一一对应这些子集。
*   **映射规则**：每个整数的二进制表示可以看作一个“选择掩码”。
    *   如果整数 $i$ 的第 $j$ 位是 $1$，则说明第 $j$ 个元素 $nums[j]$ **被选中**。
    *   如果第 $j$ 位是 $0$，则说明第 $j$ 个元素**未被选中**。
*   **流程**：
    1.  循环 $i$ 从 $0$ 到 $2^N - 1$。
    2.  在循环内部，再循环 $j$ 从 $0$ 到 $N-1$。
    3.  通过位操作 `if ((i >> j) & 1)` 检查第 $j$ 个元素是否应该加入当前子集。

### 2. 策略 II：回溯法 (最通用结构)

回溯法提供了一个通用的递归框架，适用于所有组合、排列和子集问题。

*   **决策点**：对于集合中的每一个元素 $nums[i]$，我们都有两个选择：**选择它**或**跳过它**。
*   **核心结构**：回溯函数 `dfs` 必须在每一步做出记录。
*   **回溯逻辑**：
    1.  **记录当前路径**：在 `dfs` 函数入口处，立即将当前 `path` 记录为一个子集。这是因为空集、单元素集、多元素集都在不同的递归深度被发现。
    2.  **选择/探索**：遍历剩余元素 $i$：将 $nums[i]$ 加入 `path`，递归调用 `dfs`，起始点设为 $i+1$ (确保不重复)。
    3.  **回退**：将 $nums[i]$ 移出 `path`。

### 3. C 语言的工程考量 (内存安全)

对于 C 语言的实现，无论使用回溯法还是位运算，核心问题都是管理 $2^N$ 个结果的内存：

1.  **动态扩容**：绝不能使用硬编码的容量上限。必须使用 **`realloc` 和容量翻倍**的策略来动态扩展结果数组 (`int** sets` 和 `int* returnColumnSizes`)。
2.  **内存清理**：所有在函数内部 `malloc` 的临时数组（例如回溯法的 `path` 栈）必须在函数返回前用 `free()` 释放，避免内存泄漏。
3.  **最终返回**：返回给调用者的结果集必须是干净、紧凑的动态分配内存。

### Way Of C

> source code

```c
/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
#define INITIAL_CAPACITY 1024

// 辅助函数：翻倍扩容，并检查 realloc 失败
static void* realloc_check(void* ptr, size_t new_size) {
    void* new_ptr = realloc(ptr, new_size);
    if (!new_ptr) {
        fprintf(stderr, "Fatal: Memory reallocation failed.\n");
        exit(EXIT_FAILURE);
    }
    return new_ptr;
}

static void dfs(int *nums, int size, int start, int *path,
                int len, int ***sets_ptr, int *count_ptr, 
                int **sizes_ptr, int *capacity_ptr)
{
    // 1. 记录当前路径 (空集也是在根节点被记录的)
    // 检查容量是否足够
    if (*count_ptr >= *capacity_ptr) {
        *capacity_ptr *= 2;
        
        // 扩容 sets (int**)
        *sets_ptr = realloc_check(*sets_ptr, (*capacity_ptr) * sizeof(int *));
        
        // 扩容 sizes (int*)
        *sizes_ptr = realloc_check(*sizes_ptr, (*capacity_ptr) * sizeof(int));
    }

    // 存储结果
    int current_idx = *count_ptr;
    
    // 分配空间并复制当前路径
    (*sets_ptr)[current_idx] = (int*)malloc(len * sizeof(int));
    // NOTE: len 可以为 0 (空集)，malloc(0) 通常是有效的。
    if (len > 0 && !(*sets_ptr)[current_idx]) { /* Handle malloc failure */ return; } 
    
    // 只有在 len > 0 时才需要 memcpy
    if (len > 0) {
        memcpy((*sets_ptr)[current_idx], path, len * sizeof(int));
    }
    
    (*sizes_ptr)[current_idx] = len;
    (*count_ptr)++;
    
    // 2. 递归主循环 (选择/回溯)
    for (int i = start; i < size; i++) {
        path[len] = nums[i];
        dfs(nums, size, i + 1, path, len + 1, sets_ptr, count_ptr, sizes_ptr, capacity_ptr);
        // 回溯：无需 pop_back，因为下一轮循环会覆盖 path[len]
    }
}


int** subsets(int* nums, int numsSize, int* returnSize, int** returnColumnSizes) 
{
    if (numsSize < 0) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }
    
    int capacity = INITIAL_CAPACITY;
    int count = 0;
    
    // 1. 初始化结果数组和临时路径栈
    int **sets = (int**)malloc(capacity * sizeof(int *));
    int *sizes = (int*)malloc(capacity * sizeof(int));
    int *path = (int*)malloc(numsSize * sizeof(int)); // 路径栈的最大长度为 numsSize
    
    if (!sets || !sizes || !path) {
        free(sets); free(sizes); free(path);
        *returnSize = 0; *returnColumnSizes = NULL;
        return NULL;
    }

    // 2. 执行回溯：从索引 0 开始
    dfs(nums, numsSize, 0, path, 0, &sets, &count, &sizes, &capacity);

    // 3. 结果整理与清理局部内存

    // **修复泄漏**：释放临时路径栈
    free(path); 

    // 缩小结果数组到实际大小
    if (count > 0) {
        sets = (int**)realloc_check(sets, count * sizeof(int *));
        sizes = (int*)realloc_check(sizes, count * sizeof(int));
    } else {
        // count 永远不会是 0，因为空集总是存在，但出于安全考虑
        free(sets); sets = NULL;
        free(sizes); sizes = NULL;
    }
    
    // 4. 返回值赋值
    *returnSize = count;
    *returnColumnSizes = sizes;
    return sets;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> results;
        int n = nums.size();
        
        // 总共有 2^n 个子集，我们枚举 [0, 2^n - 1]
        int total_subsets = 1 << n;

        for (int i = 0; i < total_subsets; ++i) {
            vector<int> path;
            // 检查 i 的每一位
            for (int j = 0; j < n; ++j) {
                // 如果第 j 位是 1，则选择 nums[j]
                if ((i >> j) & 1) {
                    path.push_back(nums[j]);
                }
            }
            results.push_back(path);
        }

        return results;
    }
};
```

# Question

[combinations](https://leetcode.cn/problems/combinations/)



# Answer

> solution

组合问题 (回溯法)

### 1. 核心挑战与策略

*   **挑战**：从 $n$ 个元素中选择 $k$ 个，必须避免重复组合（例如，如果选择了 $\{1, 2\}$，就不能再选择 $\{2, 1\}$）。
*   **策略**：**回溯法**。通过递归函数和严格控制起始点来生成组合。

### 2. 回溯状态定义

回溯函数 `dfs` 必须携带所有必要的信息来做出决策和剪枝：

*   `n`：最大数字限制。
*   `k`：目标组合长度（或剩余需求量）。
*   `start`：当前层循环的**起始数字**。这是避免重复的关键。
*   `path`：当前正在构建的组合。

### 3. 严格的决策流程

#### A. 递归函数体

在 `dfs` 函数中，我们在一个 `for` 循环中遍历当前层的所有可能选择：

$$\text{for } i \leftarrow start \text{ to } n \text{ do:}$$

1.  **做出选择 (Select)**：将 $i$ 加入到 `path` 中。
2.  **探索 (Explore)**：递归调用下一层 `dfs`。下一层的起始点必须是 $i+1$，确保我们只选择比 $i$ 大的数字，从而避免重复组合（例如，选完 1 后，下一层从 2 开始，绝不会再选 1）。
3.  **回退 (Unselect/Backtrack)**：递归返回后，将 $i$ 从 `path` 中移除，恢复到上一次的状态。

#### B. 终止条件 (Termination)

当当前构建的 `path` 长度达到目标 $k$ 时，找到一个有效组合，将其添加到结果集中，并返回。

#### C. 剪枝优化 (Pruning)

为了提高效率，在进入循环之前，检查是否还有可能找到一个有效的组合：

$$\text{如果 剩下的元素数量 } (n - start + 1) < \text{ 还需要选择的元素数量 } (k - len) \text{，则返回。}$$

这个剪枝操作能提前终止无效的搜索分支，显著提升性能。

### 4. C 语言的工程考量

C 语言的解决思路需要增加额外的工程步骤，以应对其缺乏容器和自动内存管理的问题：

1.  **路径管理**：使用一个固定大小的 `path` 数组（或栈）和长度变量来模拟 `vector`。
2.  **结果存储**：由于不知道最终组合的数量，必须使用 **`realloc` 和动态容量翻倍策略**来存储最终结果集 (`results` 和 `returnColumnSizes`)。
3.  **内存清理**：必须在 `combine` 函数退出前，**释放所有局部 `malloc` 内存**（如 `path` 数组），同时确保返回给调用者的结果是正确分配的。

### Way Of C

> source code

```c
#define INITIAL_CAPACITY 1024

// 辅助函数：如果容量不足，翻倍扩容
static void* realloc_check(void* ptr, size_t new_size) {
    void* new_ptr = realloc(ptr, new_size);
    if (!new_ptr) {
        fprintf(stderr, "Fatal: Memory reallocation failed.\n");
        exit(EXIT_FAILURE);
    }
    return new_ptr;
}

static void dfs(int n, int k, int start, int *path, int len,
                int ***results_ptr, int *count_ptr, 
                int **col_sizes_ptr, int *capacity_ptr)
{
    // 剪枝优化：如果剩下的元素不足以凑齐 k 个，直接返回
    if (n - start + 1 < k - len) {
        return;
    }
    
    // 终止条件：路径长度达到 k
    if (len == k) {
        // 1. 检查容量是否足够，如果不足，翻倍扩容
        if (*count_ptr >= *capacity_ptr) {
            *capacity_ptr *= 2;
            
            // 扩容 results (int**)
            *results_ptr = realloc_check(*results_ptr, (*capacity_ptr) * sizeof(int *));
            
            // 扩容 col_sizes (int*)
            *col_sizes_ptr = realloc_check(*col_sizes_ptr, (*capacity_ptr) * sizeof(int));
        }

        // 2. 存储结果
        int current_idx = *count_ptr;
        
        // 分配空间并复制当前路径
        (*results_ptr)[current_idx] = (int*)malloc(k * sizeof(int));
        if (!(*results_ptr)[current_idx]) { /* Handle malloc failure */ return; } 
        memcpy((*results_ptr)[current_idx], path, k * sizeof(int));
        
        (*col_sizes_ptr)[current_idx] = k;
        (*count_ptr)++;
        return;
    } 
    
    // 回溯主循环
    for (int i = start; i <= n; i++) {
        path[len] = i;
        dfs(n, k, i + 1, path, len + 1, results_ptr, count_ptr, col_sizes_ptr, capacity_ptr);
    }
}


int** combine(int n, int k, int* returnSize, int** returnColumnSizes) {
    
    if (k <= 0 || k > n) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }
    
    int capacity = INITIAL_CAPACITY;
    int count = 0;
    
    // 1. 初始化结果数组 (使用初始容量)
    int **results = (int**)malloc(capacity * sizeof(int *));
    int *col_sizes = (int*)malloc(capacity * sizeof(int));
    int *path = (int*)malloc(k * sizeof(int));
    
    if (!results || !col_sizes || !path) {
        free(results); free(col_sizes); free(path);
        *returnSize = 0; *returnColumnSizes = NULL;
        return NULL;
    }

    // 2. 执行回溯
    dfs(n, k, 1, path, 0, &results, &count, &col_sizes, &capacity);

    // 3. 结果整理与清理局部内存

    // 释放临时路径栈
    free(path); 

    // 缩小结果数组到实际大小 (可选，但推荐清理多余容量)
    if (count > 0) {
        results = (int**)realloc_check(results, count * sizeof(int *));
        col_sizes = (int*)realloc_check(col_sizes, count * sizeof(int));
    } else {
        // 如果 count == 0，结果数组为空
        free(results); results = NULL;
        free(col_sizes); col_sizes = NULL;
    }
    
    // 4. 返回值赋值
    *returnSize = count;
    *returnColumnSizes = col_sizes;
    return results;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
private:
    vector<vector<int>> results;
    vector<int> current_path;

    void dfs(int n, int k, int start) {
        // 剪枝优化: 如果剩余的元素数量不够凑齐 k 个，直接返回
        // (n - start + 1) + current_path.size() < k
        // 即: n - start + 1 < k - current_path.size()
        if (n - start + 1 < k - current_path.size()) {
            return;
        }

        // 终止条件：路径长度已达 k
        if (current_path.size() == k) {
            results.push_back(current_path);
            return;
        }

        // 遍历所有可能的选择
        for (int i = start; i <= n; i++) {
            current_path.push_back(i);
            // 递归到下一层，起始点为 i + 1 (避免重复)
            dfs(n, k, i + 1);
            // 回溯：撤销选择
            current_path.pop_back();
        }
    }

public:
    vector<vector<int>> combine(int n, int k) {
        results.clear();
        current_path.clear();
        if (k <= 0 || k > n) return results;
        
        dfs(n, k, 1);
        return results;
    }
};
```

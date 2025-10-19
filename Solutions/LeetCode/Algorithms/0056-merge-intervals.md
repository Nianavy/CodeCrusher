# Question

[merge-intervals](https://leetcode.cn/problems/merge-intervals/)



# Answer

> solution

### **解决思路总结：排序与线性合并**

**格言：** “如果你不能解决问题，那就先把它排好序。排序往往能将混乱简化为可管理的一维流程。”

**核心思想：**
区间合并问题的复杂性在于区间的顺序是随机的。一旦我们按照区间的起始点进行排序，我们就可以保证所有潜在重叠的或需要合并的区间都将紧密地排列在一起。这使得我们能够通过一次线性扫描，高效地完成合并。

1.  **预处理：按起始点排序 (O(N log N))**
    *   首先，对所有输入区间集合，以每个区间的**起始点**为主要关键字进行升序排序。
    *   **重要性：** 排序保证了我们在遍历时，如果当前区间 `[l, r]` 的结束点 `r` 能够覆盖下一个区间 `[next_start, next_end]`，那么它也能覆盖所有在这两者之间的、起始点更小的区间。

2.  **线性扫描与合并 (O(N))**
    *   初始化：选取排序后的第一个区间作为当前的“正在合并”的区间 `[current_start, current_end]`。
    *   迭代：从第二个区间开始，依次与 `[current_start, current_end]` 进行比较：

        *   **重叠/相接条件：** 如果下一个区间 `next_start` 小于或等于 `current_end`，则它们重叠或相接，可以合并。
            *   **操作：** 保持 `current_start` 不变，更新 `current_end = max(current_end, next_end)`。我们只需要扩展右边界。

        *   **不重叠条件：** 如果下一个区间 `next_start` 大于 `current_end`，说明当前正在合并的区间 `[current_start, current_end]` 已经达到其最大范围，与后续区间无法连接。
            *   **操作：** 将完成合并的 `[current_start, current_end]` 结果推入最终列表。然后，将 `[next_start, next_end]` 设置为新的 `[current_start, current_end]`，开始下一轮合并。

3.  **最终处理：**
    *   在整个数组遍历完成后，最后一个正在合并的区间（`[current_start, current_end]`）尚未被推入结果列表。因此，在循环结束后，必须将这个最终结果也加入到列表中。

**实用主义的体现：**
该方法避免了任何复杂的嵌套循环或数据结构。它将问题简化为“一次排序，一次线性遍历”。这种方法既保证了 O(N log N) 的最优时间复杂度，又提供了清晰、可读的逻辑流程，符合我对代码“实用、简洁”的要求。

### Way Of C

> source code

```c
// 辅助函数：查找最大值
static inline int c_max(int a, int b) {
    return a > b ? a : b;
}

// 比较函数：用于 qsort 对二维数组（int**）的行进行排序。
// 比较的是两个区间（即两个 int* 指向的数组）。
static int compare_intervals(const void *a, const void *b)
{
    // a 和 b 是指向 int* 的指针 (即 int***)
    const int *arr_a = *(const int **)a;
    const int *arr_b = *(const int **)b;
    
    // 比较它们的起始点 (索引 0)
    return arr_a[0] - arr_b[0];
}

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** merge(int** intervals, int intervalsSize, int* intervalsColSize, int* returnSize, int** returnColumnSizes)
{
    if (intervalsSize == 0) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }
    
    // 1. 排序：使用 qsort 对传入的 intervals 数组（一个 int**）进行原地排序。
    // qsort 需要知道元素的大小，这里是 sizeof(int*)，因为 intervals 是一个 int* 数组。
    qsort(intervals, intervalsSize, sizeof(int*), compare_intervals);

    // 2. 初始化结果存储空间 (预分配最大可能空间)
    // 结果数组不会超过 intervalsSize 个元素，所以先按最大尺寸分配。
    int max_capacity = intervalsSize;
    int **result_intervals = (int**)malloc(max_capacity * sizeof(int*));
    if (result_intervals == NULL) {
        // 内存分配失败
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }
    
    // 3. 设置第一个区间作为当前正在合并的区间
    int current_start = intervals[0][0];
    int current_end = intervals[0][1];
    int result_index = 0; // 记录结果数组中已有的区间数量

    // 4. 线性合并
    for (int i = 1; i < intervalsSize; i++) {
        int next_start = intervals[i][0];
        int next_end = intervals[i][1];

        if (next_start <= current_end) {
            // 重叠，更新结束点
            current_end = c_max(current_end, next_end);
        } else {
            // 不重叠，当前区间合并完成，存储到结果中
            
            // 为结果中的区间分配内存 [start, end]
            result_intervals[result_index] = (int*)malloc(2 * sizeof(int));
            result_intervals[result_index][0] = current_start;
            result_intervals[result_index][1] = current_end;
            result_index++;
            
            // 开始新的合并区间
            current_start = next_start;
            current_end = next_end;
        }
    }
    
    // 5. 存储最后一个正在合并的区间
    result_intervals[result_index] = (int*)malloc(2 * sizeof(int));
    result_intervals[result_index][0] = current_start;
    result_intervals[result_index][1] = current_end;
    result_index++;

    // 6. 调整内存大小并设置返回参数
    *returnSize = result_index;
    
    // 重新调整 result_intervals 数组的大小到实际需要的尺寸 (可选，但更规范)
    // result_intervals = (int**)realloc(result_intervals, *returnSize * sizeof(int*));
    
    // 设置返回列大小数组 (每个区间有两个元素)
    *returnColumnSizes = (int*)malloc(*returnSize * sizeof(int));
    for (int i = 0; i < *returnSize; i++) {
        (*returnColumnSizes)[i] = 2;
    }
    
    return result_intervals;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::vector<std::vector<int>> merge(std::vector<std::vector<int>>& intervals) {
        std::vector<std::vector<int>> res;

        // 鲁棒性检查：如果输入为空，直接返回空结果。
        if (intervals.empty()) {
            return res;
        }

        // 第一步：根据区间的起始点进行排序。
        // std::sort 默认会按第一个元素（即起始点）排序。
        std::sort(intervals.begin(), intervals.end());

        // 使用第一个区间作为当前正在合并的区间
        int current_start = intervals[0][0];
        int current_end = intervals[0][1];
        
        // 从第二个区间开始遍历
        for (size_t i = 1; i < intervals.size(); ++i) {
            int next_start = intervals[i][0];
            int next_end = intervals[i][1];

            // 检查是否重叠或相接 (next_start <= current_end)
            if (next_start <= current_end) {
                // 重叠，合并：更新当前区间的结束点为两者中较大的
                current_end = std::max(current_end, next_end);
            } else {
                // 不重叠，当前区间 [current_start, current_end] 合并完成，推入结果
                res.push_back({current_start, current_end});
                
                // 开始新的合并区间
                current_start = next_start;
                current_end = next_end;
            }
        }

        // 循环结束后，将最后一个（或唯一一个）正在合并的区间推入结果
        res.push_back({current_start, current_end});
        
        return res;
    }
};
```

# Question

[insert-interval](https://leetcode.cn/problems/insert-interval/)



# Answer

> solution

### **解决思路总结：O(N) 线性三段式扫描**

**格言：** “如果你知道数据的顺序，就不要浪费时间去重新计算。利用现有的结构，一次性走完。”

**核心思想：**
由于输入 `intervals` 数组是按起始点排序的（这是区间问题的常见约定，也是高效解法的前提），我们无需进行全局排序。只需利用线性扫描，将整个过程分解为三个清晰、不重叠的阶段。整个数组的遍历指针 `i` 或 `k` 只向前移动，保证了 O(N) 的线性时间复杂度。

**三个阶段：**

1.  **左侧非重叠部分 (Left Non-Overlapping)**
    *   **目标：** 找出所有在新区间 `newInterval` **左侧**且**完全不重叠**的现有区间。
    *   **判断条件：** 当前区间 `intervals[i]` 的结束点 `intervals[i][1]` 严格小于 `newInterval` 的起始点 `newInterval[0]`。
    *   **操作：** 将这些区间直接添加到结果列表，并推进指针 `i`。

2.  **中间重叠与合并部分 (Middle Overlapping/Merging)**
    *   **目标：** 找出所有与 `newInterval` **有交集**的现有区间，并将它们合并到 `newInterval` 中。
    *   **判断条件：** 当前区间 `intervals[i]` 的起始点 `intervals[i][0]` 小于或等于 `newInterval` 的结束点 `newInterval[1]`。
    *   **操作：** 在这个阶段，`newInterval` 临时作为我们正在构建的合并区间。
        *   更新新区间的起始点：`newInterval.start = min(newInterval.start, intervals[i].start)`
        *   更新新区间的结束点：`newInterval.end = max(newInterval.end, intervals[i].end)`
        *   不断推进指针 `i`，直到找到第一个不重叠的区间。

    *   **插入：** 循环结束后，将**合并完成的** `newInterval` 推入结果列表。

3.  **右侧非重叠部分 (Right Non-Overlapping)**
    *   **目标：** 将所有在合并后的 `newInterval` **右侧**且不重叠的剩余现有区间直接加入结果。
    *   **判断条件：** 简单地将剩余的 `intervals[i]` 元素全部推入结果。
    *   **操作：** 将剩余区间添加到结果列表，并推进指针 `i` 直到数组末尾。

**实用主义的体现：**
这种方法是解决“已排序区间插入”问题的标准、高效范式。它避免了先将新区间插入再进行全局排序的浪费行为，直接利用了输入数据结构，确保了算法的效率达到线性最优，简洁且符合工程实践。

### Way Of C

> source code

```c
static inline int c_min(int a, int b) {
    return a < b ? a : b;
}

static inline int c_max(int a, int b) {
    return a > b ? a : b;
}

/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** insert(int** intervals, int intervalsSize, int* intervalsColSize, int* newInterval, 
             int newIntervalSize, int* returnSize, int** returnColumnSizes)
{
    // 结果数组的最大可能大小是 intervalsSize + 1
    int max_capacity = intervalsSize + 1;
    int **result_intervals = (int**)malloc(max_capacity * sizeof(int*));
    if (result_intervals == NULL) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }
    
    int result_index = 0;
    int i = 0;

    // --- 1. 左侧不重叠部分 ---
    // 条件：当前区间的结束点 intervals[i][1] < newInterval[0]
    while (i < intervalsSize && intervals[i][1] < newInterval[0]) {
        // 复制当前区间到结果
        result_intervals[result_index] = (int*)malloc(2 * sizeof(int));
        result_intervals[result_index][0] = intervals[i][0];
        result_intervals[result_index][1] = intervals[i][1];
        result_index++;
        i++;
    }

    // --- 2. 中间重叠/合并部分 ---
    // 条件：当前区间的起始点 intervals[i][0] <= newInterval[1]
    while (i < intervalsSize && intervals[i][0] <= newInterval[1]) {
        // 更新 newInterval 的起始点和结束点
        newInterval[0] = c_min(newInterval[0], intervals[i][0]);
        newInterval[1] = c_max(newInterval[1], intervals[i][1]);
        i++;
    }

    // 将合并后的 newInterval 加入结果
    result_intervals[result_index] = (int*)malloc(2 * sizeof(int));
    result_intervals[result_index][0] = newInterval[0];
    result_intervals[result_index][1] = newInterval[1];
    result_index++;

    // --- 3. 右侧不重叠部分 ---
    // 将剩余的区间直接复制到结果
    while (i < intervalsSize) {
        result_intervals[result_index] = (int*)malloc(2 * sizeof(int));
        result_intervals[result_index][0] = intervals[i][0];
        result_intervals[result_index][1] = intervals[i][1];
        result_index++;
        i++;
    }

    // 设置返回参数
    *returnSize = result_index;
    
    // 设置返回列大小数组 (每个区间有两个元素)
    *returnColumnSizes = (int*)malloc(*returnSize * sizeof(int));
    if (*returnColumnSizes == NULL) {
        // 假设内存分配不会失败，否则需要复杂的清理逻辑
    }
    for (int k = 0; k < *returnSize; k++) {
        (*returnColumnSizes)[k] = 2;
    }
    
    // 返回结果（通常这里应该 realloc 调整 result_intervals 的大小，但为简化 C 代码，我们依赖调用者释放 max_capacity 内存）
    return result_intervals;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 插入并合并区间：利用输入 intervals 数组已排序的特性进行 O(N) 线性合并。
     */
    std::vector<std::vector<int>> insert(std::vector<std::vector<int>>& intervals, std::vector<int>& newInterval) {
        std::vector<std::vector<int>> res;
        int n = intervals.size();
        int i = 0;
        
        // 1. 左侧不重叠部分：将所有在 newInterval 左边且不重叠的区间直接加入结果
        // 条件：当前区间的结束点 a[i][1] < newInterval[0]
        while (i < n && intervals[i][1] < newInterval[0]) {
            res.push_back(intervals[i]);
            i++;
        }

        // 2. 中间重叠/合并部分：合并所有与 newInterval 重叠的区间
        // 条件：当前区间的起始点 a[i][0] <= newInterval[1]
        while (i < n && intervals[i][0] <= newInterval[1]) {
            // 更新 newInterval 的起始点：取两者起始点的最小值
            newInterval[0] = std::min(newInterval[0], intervals[i][0]);
            // 更新 newInterval 的结束点：取两者结束点的最大值
            newInterval[1] = std::max(newInterval[1], intervals[i][1]);
            i++;
        }

        // 将合并后的 newInterval (现在它可能是一个更大的区间) 加入结果
        res.push_back(newInterval);

        // 3. 右侧不重叠部分：将所有在合并后的 newInterval 右边且不重叠的区间直接加入结果
        while (i < n) {
            res.push_back(intervals[i]);
            i++;
        }

        return res;
    }
};
```

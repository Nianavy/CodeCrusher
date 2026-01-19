# Question

[maximum-gap](https://leetcode.cn/problems/maximum-gap/)



# Answer

> solution

**解题思路总结**

1.  **直面核心问题**：问题本质是“在未排序数组中找出最大间隙”。直接排序再遍历是 O(N log N)，简单可靠。桶排序是 O(N)，但引入了复杂性。

2.  **识别关键缺陷**：你提供的代码（以及类似的常见实现）依赖**浮点数除法**来计算桶大小和索引。这是**脆弱性**的根源：浮点数精度问题会导致索引计算错误，引发数组越界或逻辑错误。

3.  **整数化重构**：彻底移除浮点运算。使用纯整数算术计算桶大小：
    *   桶大小 = `(最大值 - 最小值 + (n-2)) / (n-1)` 或类似，但更清晰的方式是 `(最大值 - 最小值) / (n-1)` 并处理余数以确保覆盖全部区间。确保 `bucket_size > 0`。

4.  **数据结构去冗余**：
    *   使用两个独立的 `min_bucket` 和 `max_bucket` 数组/向量，而不是包含 `used` 标志的 `Range` 结构体。这样内存更紧凑，访问更高效。
    *   桶的数量设为 `n`（数组长度），因为理论上最多有 `n-1` 个有效桶，但多一个不影响，且简化了索引计算。

5.  **算法流程统一**：
    *   **不用特殊处理最小值**：最小值会被自动分配到第 0 号桶（`(min - min) / bucket_size = 0`）。
    *   **不用显式处理空桶**：初始化 `min_bucket` 为 `INT_MAX`，`max_bucket` 为 `INT_MIN`。在遍历计算间隙时，跳过 `min_bucket[i] == INT_MAX` 的桶即可。这比标记结构体更简单。
    *   **间隙计算**：只关心**非空桶**的间隔。遍历桶数组，维护上一个非空桶的 `max`，当前桶的 `min` 减去它就是候选间隙。

6.  **边界与内存安全**：
    *   处理空/单元素输入。
    *   C 版本中 `malloc` 后必须检查返回值，并在函数末尾 `free`。
    *   确保索引计算不会越界（例如，最大值对应的桶索引可能等于 `n`，需修正为 `n-1`）。

**核心哲学体现**：
*   **实用主义**：优先选择可靠、简单的整数运算，而非浮点数。
*   **简洁性**：消除浮点数、消除复杂条件、使用清晰命名的数据结构。
*   **健壮性**：纯整数运算无精度损失，内存管理得当，避免未定义行为。

### Way Of C

> source code

```c
// 简单的辅助宏
#define MIN(a, b) ((a) < (b) ? (a) : (b))
#define MAX(a, b) ((a) > (b) ? (a) : (b))
int maximumGap(int* nums, int numsSize) {
    if (numsSize < 2) {
        return 0;
    }
    int min_val = INT_MAX;
    int max_val = INT_MIN;
    
    // 1. 找到 min 和 max
    for (int i = 0; i < numsSize; ++i) {
        if (nums[i] < min_val) min_val = nums[i];
        if (nums[i] > max_val) max_val = nums[i];
    }
    if (min_val == max_val) {
        return 0;
    }
    // 2. 计算桶大小（纯整数）
    // 必须将区间 [min, max] 分配到 numsSize - 1 个间隙中
    // 注意：这里 (max_val - min_val) + (n-1) - 1 的写法是为了向上取整，但更清晰的写法是如下：
    int range = max_val - min_val;
    int bucket_size = range / (numsSize - 1);
    if (range % (numsSize - 1) != 0) {
        bucket_size++;
    }
    // 3. 分配桶。数量可以是 numsSize。
    // C 语言中必须检查 malloc
    int *min_bucket = (int*)malloc(numsSize * sizeof(int));
    int *max_bucket = (int*)malloc(numsSize * sizeof(int));
    
    if (min_bucket == NULL || max_bucket == NULL) {
        // 内存分配失败处理
        free(min_bucket); 
        free(max_bucket); // 释放可能部分成功的指针
        return 0; // 或者是其他错误码，根据需求
    }
    // 4. 初始化桶
    for (int i = 0; i < numsSize; ++i) {
        min_bucket[i] = INT_MAX;
        max_bucket[i] = INT_MIN;
    }
    // 5. 填充桶
    for (int i = 0; i < numsSize; ++i) {
        int idx = (nums[i] - min_val) / bucket_size;
        if (idx >= numsSize) {
            idx = numsSize - 1;
        }
        if (nums[i] < min_bucket[idx]) min_bucket[idx] = nums[i];
        if (nums[i] > max_bucket[idx]) max_bucket[idx] = nums[i];
    }
    // 6. 计算最大间隙
    int max_gap = 0;
    int prev_max = max_bucket[0];
    for (int i = 1; i < numsSize; ++i) {
        if (min_bucket[i] == INT_MAX) {
            continue; // 空桶
        }
        if (min_bucket[i] - prev_max > max_gap) {
            max_gap = min_bucket[i] - prev_max;
        }
        prev_max = max_bucket[i];
    }
    free(min_bucket);
    free(max_bucket);
    
    return max_gap;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int maximumGap(vector<int>& nums) {
        int n = nums.size();
        if (n < 2) {
            return 0;
        }
        // 1. 找到全局最小值和最大值
        int min_val = INT_MAX;
        int max_val = INT_MIN;
        for (int x : nums) {
            if (x < min_val) min_val = x;
            if (x > max_val) max_val = x;
        }
        // 2. 如果所有元素相同，差值为0
        if (min_val == max_val) {
            return 0;
        }
        // 3. 计算桶的大小。使用整数除法。
        // 必须使用 n - 1 个间隔来容纳 n 个数。
        // 确保 size > 0
        int bucket_size = (max_val - min_val) / (n - 1);
        if ((max_val - min_val) % (n - 1) != 0) {
            bucket_size++; // 向上取整，确保不漏数
        }
        // 4. 初始化桶。桶的数量是 n。
        // 即使所有数分布均匀，我们最多也只有 n-1 个有效桶，但开 n 个桶的内存很简单。
        // 这里的 min_bucket 和 max_bucket 可以合并到一个 struct，
        // 但为了极致的内存局部性，我们分开声明。
        vector<int> min_bucket(n, INT_MAX);
        vector<int> max_bucket(n, INT_MIN);
        // 5. 填充桶
        for (int x : nums) {
            // 核心：使用纯整数计算桶索引
            int idx = (x - min_val) / bucket_size;
            
            // 索引可能等于 n (当 x == max_val 且整除时)
            if (idx >= n) {
                idx = n - 1;
            }
            min_bucket[idx] = min(min_bucket[idx], x);
            max_bucket[idx] = max(max_bucket[idx], x);
        }
        // 6. 寻找最大间隙
        int max_gap = 0;
        int prev_max = max_bucket[0]; // 第 0 个桶一定存在（至少包含 min_val）
        for (int i = 1; i < n; ++i) {
            // 如果桶是空的，跳过
            if (min_bucket[i] == INT_MAX) {
                continue;
            }
            // 此处的计算逻辑正确且清晰：
            // 当前桶的最小值 - 上一个非空桶的最大值
            // 这正是“跨桶”的间隙。
            max_gap = max(max_gap, min_bucket[i] - prev_max);
            prev_max = max_bucket[i];
        }
        return max_gap;
    }
};
```

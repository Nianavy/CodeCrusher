# Question

[maximum-subarray](https://leetcode.cn/problems/maximum-subarray/)



# Answer

> solution

### **动态规划 (DP) 解法思路总结：**

**格言：** “如果你有能力从当前位置开始做选择，并且知道之前最好的选择是什么，那你就没必要回头看。”

**核心思想：**
动态规划解法关注的是“以当前元素结尾的最大子数组和”。我们不需要知道这个子数组具体是什么，只需要知道它的和是多少。

1.  **定义状态：**
    *   `current_max_ending_here`：表示以当前遍历到的元素 `nums[i]` **结尾**的最大子数组和。
    *   `global_max_so_far`：表示到目前为止遍历过的所有元素中，所能找到的**全局**最大子数组和。

2.  **状态转移：**
    *   当遍历到 `nums[i]` 时，计算 `current_max_ending_here`：
        *   它要么是 `nums[i]` 自己（意味着之前的所有元素都使总和变小，不如从 `nums[i]` 重新开始），
        *   要么是 `nums[i]` 加上 `current_max_ending_here`（也就是前一个元素结尾的最大子数组和）。
        *   选择这两者中较大的作为新的 `current_max_ending_here`。
        *   数学表达式：`current_max_ending_here = max(nums[i], current_max_ending_here + nums[i])`
    *   每次更新 `current_max_ending_here` 后，用它来更新 `global_max_so_far`。
        *   数学表达式：`global_max_so_far = max(global_max_so_far, current_max_ending_here)`

3.  **初始化：**
    *   通常，`current_max_ending_here` 和 `global_max_so_far` 都初始化为数组的第一个元素 `nums[0]`。

4.  **结果：**
    *   遍历完整个数组后，`global_max_so_far` 就是最终的最大子数组和。

**简洁执念的体现：**
该方法通过两个简单变量，一次线性扫描，高效地解决了问题。它没有多余的数据结构，也没有复杂的递归，是一种“斯巴达式”的优雅。

---

### **分治 (Divide and Conquer) 解法思路总结：**

**格言：** “把一个大问题切成两半，独立解决它们，然后想办法把它们拼起来。如果拼不起来，那你的切割方式就错了。”

**核心思想：**
分治法是将一个大问题分解为多个相同类型的子问题，独立解决这些子问题，然后将子问题的解合并，得到原问题的解。对于最大子数组和问题，关键在于如何合并。

1.  **分解 (Divide)：**
    *   将数组 `nums` 从中间 `mid` 位置一分为二：左子区间 `[l, mid]` 和右子区间 `[mid+1, r]`。

2.  **解决 (Conquer)：**
    *   递归地对左右两个子区间求解。每个子区间返回一个 `Node` 结构体，包含四部分信息：
        *   `sum`：该子区间的总和。
        *   `max_total_sum`：该子区间内的最大子数组和。
        *   `max_prefix_sum`：从该子区间左端点开始的最大前缀和。
        *   `max_suffix_sum`：从该子区间右端点开始的最大后缀和。

3.  **合并 (Combine)：**
    *   这是最关键的部分。合并左右子区间的 `Node` 信息，计算出整个区间 `[l, r]` 的 `Node` 信息。
    *   **计算 `sum`：** 简单相加 `L.sum + R.sum`。
    *   **计算 `max_total_sum` (整个区间的最大子数组和)：** 有三种可能：
        1.  完全位于左子区间 (`L.max_total_sum`)。
        2.  完全位于右子区间 (`R.max_total_sum`)。
        3.  跨越中间点，由左子区间的最大后缀和与右子区间的最大前缀和组成 (`L.max_suffix_sum + R.max_prefix_sum`)。
        取这三者中的最大值。
    *   **计算 `max_prefix_sum` (从左端点开始的最大前缀和)：** 有两种可能：
        1.  完全位于左子区间 (`L.max_prefix_sum`)。
        2.  从左子区间一直延伸到右子区间 (`L.sum + R.max_prefix_sum`)。
        取这两者中的最大值。
    *   **计算 `max_suffix_sum` (从右端点开始的最大后缀和)：** 有两种可能：
        1.  完全位于右子区间 (`R.max_suffix_sum`)。
        2.  从右子区间一直延伸到左子区间 (`R.sum + L.max_suffix_sum`)。
        取这两者中的最大值。

4.  **基本情况 (Base Case)：**
    *   当子区间只有一个元素时 (`l == r`)，所有这四项值都等于该元素本身。

**简洁执念的体现：**
虽然分治方法引入了一个结构体和递归，看起来比 DP 复杂，但它通过精确定义子问题（`Node` 结构体中的四个字段），使得合并逻辑变得清晰和可操作。每个字段都只为了解决最终问题的一个小方面，没有冗余。递归深度是 `log N`，保证了效率。

### Way Of C

> source code

```c
#include <limits.h> // 引入 INT_MIN
#include <stddef.h> // 引入 size_t

// --- 辅助函数：模拟 C++ 的 std::max ---
// 直接、粗暴，但有效。C 没有内建的泛型 max，所以需要自己实现或用宏。
static int c_max(int a, int b) {
    return a > b ? a : b;
}


// --- 动态规划版本 (Optimized DP) ---
// 核心思想：
//   `current_max` 记录以当前元素 nums[i] 结尾的最大子数组和。
//   `global_max` 记录到目前为止遍历过的所有子数组中的最大和。
//   如果 `current_max` 加上 nums[i] 后比 nums[i] 自身还小，
//   说明之前的子数组和是负贡献，那就从 nums[i] 重新开始计算。
//   这样即使所有数都是负数，`global_max` 也能正确地追踪到最大的那个负数。
int maxSubArray_DP(int* nums, int numsSize) {
    if (numsSize == 0) {
        return 0; // 空数组，返回 0。根据具体需求，也可以抛出错误或返回 INT_MIN。
    }

    int current_max = nums[0]; // 初始化为第一个元素，它本身就是以自己结尾的最大子数组。
    int global_max = nums[0];  // 全局最大值也初始化为第一个元素。

    // 从第二个元素开始遍历
    for (size_t i = 1; i < numsSize; i++) {
        // 计算以 nums[i] 结尾的最大子数组和：
        // 它可以是 nums[i] 自己，
        // 也可以是 (以 nums[i-1] 结尾的最大子数组和) + nums[i]。
        // 取两者中较大的一个。
        current_max = c_max(nums[i], current_max + nums[i]);
        
        // 更新全局最大子数组和
        global_max = c_max(global_max, current_max);
    }

    return global_max;
}


// --- 分治版本 (Divide and Conquer) ---
// 分治法需要一个结构体来保存子区间的信息：
// `sum`: 该子区间的总和。
// `max_subarray_sum`: 该子区间内的最大子数组和。
// `max_prefix_sum`: 从该子区间左端点开始的最大前缀和。
// `max_suffix_sum`: 从该子区间右端点开始的最大后缀和。
// 注意：这里所有的 `max` 都不再和 `0` 比较，以正确处理全负数数组。
struct Node {
    int sum;            // 当前区间的总和
    int max_subarray_sum; // 当前区间内的最大子数组和 (即原 C++ 代码中的 `s`)
    int max_prefix_sum; // 当前区间内从左端点开始的最大前缀和 (即原 C++ 代码中的 `ls`)
    int max_suffix_sum; // 当前区间内从右端点开始的最大后缀和 (即原 C++ 代码中的 `rs`)
};

// 递归构建函数，计算并合并子区间信息
static struct Node build_segment(int* nums, int l, int r) {
    if (l == r) {
        // 基本情况：单个元素区间，所有值都是元素本身
        return (struct Node){nums[l], nums[l], nums[l], nums[l]};
    }

    int mid = l + (r - l) / 2; // 防止 (l+r) 溢出
    
    // 递归处理左右子区间
    struct Node left_node = build_segment(nums, l, mid);
    struct Node right_node = build_segment(nums, mid + 1, r);

    // 合并左右子区间的结果
    struct Node res;
    res.sum = left_node.sum + right_node.sum;

    // 计算当前区间的最大子数组和 (max_subarray_sum):
    // 1. 仅在左子区间中 (left_node.max_subarray_sum)
    // 2. 仅在右子区间中 (right_node.max_subarray_sum)
    // 3. 跨越中间点，由左子区间最大后缀和 + 右子区间最大前缀和组成
    res.max_subarray_sum = c_max(c_max(left_node.max_subarray_sum, right_node.max_subarray_sum),
                                   left_node.max_suffix_sum + right_node.max_prefix_sum);
    
    // 计算当前区间的最大前缀和 (max_prefix_sum):
    // 1. 仅在左子区间中 (left_node.max_prefix_sum)
    // 2. 左子区间总和 + 右子区间的最大前缀和
    res.max_prefix_sum = c_max(left_node.max_prefix_sum, left_node.sum + right_node.max_prefix_sum);
    
    // 计算当前区间的最大后缀和 (max_suffix_sum):
    // 1. 仅在右子区间中 (right_node.max_suffix_sum)
    // 2. 右子区间总和 + 左子区间的最大后缀和
    res.max_suffix_sum = c_max(right_node.max_suffix_sum, right_node.sum + left_node.max_suffix_sum);
    
    return res;
}

int maxSubArray_DivideAndConquer(int* nums, int numsSize) {
    if (numsSize == 0) {
        return 0; // 空数组，返回 0。
    }
    struct Node result = build_segment(nums, 0, numsSize - 1);
    return result.max_subarray_sum;
}

// --- 统一函数签名，方便调用 ---
// 根据你的需求，实际的 `maxSubArray` 函数可以选择调用 DP 或分治版本。
// 这里我将其命名为 `maxSubArray`，默认使用更高效的 DP 版本。
int maxSubArray(int* nums, int numsSize) {
    // 默认使用动态规划版本，因为它更高效 (O(N) vs O(N log N))
    return maxSubArray_DP(nums, numsSize);
    // 如果需要使用分治版本，可以改为：
    // return maxSubArray_DivideAndConquer(nums, numsSize);
}
```

---


### Way Of C++

> source code

```c++
// 动态规划解法：Kadane 算法
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int res = INT_MIN;  // 初始化结果为最小整数，用于记录全局最大子数组和
        int last = 0;       // last 表示以当前元素结尾的最大子数组和

        for (int i = 0; i < nums.size(); i++) {
            // 状态转移：如果之前的 last 是负数，就舍弃（加0），否则加上 last
            last = nums[i] + max(last, 0);
            // 更新全局最大值
            res = max(res, last);
        }

        return res;
    }
};

// 分治解法：基于线段树思想，每个区间维护四个值
class Solution {
public:
    // 定义区间信息结构体
    struct Node {
        int sum;  // 整个区间的元素总和
        int s;    // 该区间内最大子数组和（答案）
        int ls;   // 该区间内以左端点开头的最大子数组和（最大前缀和）
        int rs;   // 该区间内以右端点结尾的最大子数组和（最大后缀和）
    };

    // 递归构建区间信息 [l, r]
    Node build(vector<int>& nums, int l, int r) {
        // 叶子节点：区间只有一个元素
        if (l == r) {
            int v = max(nums[l], 0);  // 注意：这里将负数视为0（因为后续只在非全负时调用）
            return {
                nums[l],  // sum = 该元素本身
                v,        // s = max(该元素, 0)
                v,        // ls = max(该元素, 0)
                v         // rs = max(该元素, 0)
            };
        }

        int mid = (l + r) >> 1;  // 分治中点
        auto L = build(nums, l, mid);        // 左子区间信息
        auto R = build(nums, mid + 1, r);    // 右子区间信息

        Node res;
        // 合并左右区间
        res.sum = L.sum + R.sum;                          // 总和直接相加
        res.s = max({L.s, R.s, L.rs + R.ls});             // 最大子数组和：三种可能（左、右、跨中点）
        res.ls = max(L.ls, L.sum + R.ls);                 // 最大前缀和：要么全在左，要么左全部 + 右的前缀
        res.rs = max(R.rs, R.sum + L.rs);                 // 最大后缀和：要么全在右，要么右全部 + 左的后缀

        return res;
    }

    int maxSubArray(vector<int>& nums) {
        // 特判：如果所有数都为负数，则不能取空子数组（题目要求非空），直接返回最大负数
        int res = INT_MIN;
        for (auto x : nums) {
            res = max(res, x);
        }
        if (res < 0) {
            return res;  // 全为负数时，返回最大的那个负数
        }

        // 否则，调用分治构建，此时允许子数组和为0（即可以“跳过”负贡献）
        auto t = build(nums, 0, nums.size() - 1);
        return t.s;  // 返回整个区间的最大子数组和
    }
};
```

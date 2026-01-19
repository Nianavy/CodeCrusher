# Question

[maximum-product-subarray](https://leetcode.cn/problems/maximum-product-subarray/)



# Answer

> solution

# 解决“最大子数组乘积”问题的思路

## 核心思想

**维护两个状态变量**，遍历数组时跟踪以当前位置结尾的子数组**最大乘积**和**最小乘积**。

## 步骤

1. **初始化**：
   - 最大乘积 `max_so_far` 和当前最大乘积 `curr_max` 设为第一个元素值 `nums[0]`
   - 当前最小乘积 `curr_min` 也设为 `nums[0]`

2. **遍历数组**（从下标 1 开始）：
   - 对于每个元素 `a = nums[i]`
   - 计算三种可能的新乘积：
     - `a` 本身
     - `curr_max * a`
     - `curr_min * a`（因为负负得正）
   - 更新 `curr_max` 为以上三者的最大值
   - 更新 `curr_min` 为以上三者的最小值
   - 用 `curr_max` 更新全局最大乘积 `max_so_far`

3. **返回全局最大乘积**

## 复杂度分析

- **时间复杂度**：O(n)，只需一次遍历
- **空间复杂度**：O(1)，只用了三个整数变量

## 关键优势

- **连续**：子数组必须是连续的（这是题目的关键约束）
- **利用负数**：通过维护最小乘积，当遇到负数时能“反转”为最大乘积
- **线性扫描**：一次遍历解决，无需嵌套循环

## 适用场景

- 整数数组（可能包含正数、负数、0）
- 需要处理 0 的情况（实际代码中 0 会自然重置乘积）
- 空数组返回 0 进行边界处理

这个思路本质上是 **O(n) 状态机**，用最少的状态变量解决问题。

### Way Of C

> source code

```c
static inline int min(int a, int b) {
    return a < b ? a : b;
}
static inline int max(int a, int b) {
    return a > b ? a : b;
}
int maxProduct(int* nums, int numsSize) {
    if (numsSize == 0) {
        return 0;  // 实际问题中，空数组乘积无意义，保守返回 0
    }
    int res = nums[0];
    int f = nums[0];  // max product ending at i
    int g = nums[0];  // min product ending at i
    for (int i = 1; i < numsSize; i++) {
        int a = nums[i];
        int fa = f * a;
        int ga = g * a;
        f = max(a, max(fa, ga));
        g = min(a, min(fa, ga));
        res = max(res, f);
    }
    return res;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int maxProduct(std::vector<int>& nums) {
        if (nums.empty()) {
            return 0;  // 空数组：返回 0
        }
        int res = nums[0];
        int f = nums[0];
        int g = nums[0];
        for (size_t i = 1; i < nums.size(); ++i) {
            int a = nums[i];
            int fa = f * a;
            int ga = g * a;
            f = std::max(a, std::max(fa, ga));
            g = std::min(a, std::min(fa, ga));
            res = std::max(res, f);
        }
        return res;
    }
};
```

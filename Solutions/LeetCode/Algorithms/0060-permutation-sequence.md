# Question

[permutation-sequence](https://leetcode.cn/problems/permutation-sequence/)



# Answer

> solution

### **解决思路总结：基于阶乘的计数法 (Cantor Expansion 逆运算)**

**格言：** “排列是数字的组合。与其暴力尝试，不如用数学的确定性来定位。”

**核心思想：**
该问题要求在所有 $N!$ 种排列中，找到第 $K$ 个。由于排列是按字典序排序的，我们可以利用阶乘来确定每一位应该放置哪个数字，这是一种贪心和分治的思想。

1.  **预计算与预处理：**
    *   **阶乘：** 预先计算出 $0!$ 到 $N!$ 的值。这是划分“组”的基础。
    *   **K 的调整：** 将输入的 $K$（通常是 1-based 索引）调整为 0-based 索引：`k = k - 1`。

2.  **分治决策（从左到右）：**
    *   我们从排列的最高位（最左侧）开始确定数字。对于当前要确定的位置 `i`，其后的 $(N-i-1)$ 个数字可以组成 $(N-i-1)!$ 种排列。我们将所有可能的排列分成若干个大小为 $(N-i-1)!$ 的组。
    *   **确定组索引：** 通过计算 `index = k / fact_group`，我们确定了当前位应该选择未使用的数字列表中的第 `index` 个数字。这个 `index` 表示需要跳过多少个完整的组。
    *   **选择数字：** 在当前未使用的数字列表中，选择第 `index` 个数字作为排列的当前位。这是**贪心**的一步，因为这是满足 $K$ 要求的最小选择。
    *   **更新 K：** 我们已经跳过了 `index` 组，所以更新 `k = k % fact_group`。新的 `k` 值代表了在被选定数字的那个组中，我们还需要向下查找的相对位置。

3.  **追踪可用数字：**
    *   每确定并使用一个数字，必须将其从可用数字列表中移除或标记为已使用，以确保后续选择的数字是唯一的。
    *   **性能瓶颈：** 在 $N$ 次循环中，查找第 `index` 个未使用的数字需要 O(N) 时间，导致总时间复杂度为 O($N^2$)。

4.  **重复：**
    *   重复这个过程 $N$ 次，直到所有 $N$ 个位置都被确定。

**实用主义的体现：**
尽管该方法的复杂度是 O($N^2$)，但在 $N \le 9$ 这种约束下，它比 O($N!$) 的暴力解法快得多，且其数学原理非常稳固。这种通过阶乘划分搜索空间的方法，是解决排列序列问题的最优雅和高效的“好品味”方案。

### Way Of C

> source code

```c
/**
 * 求解第 K 个排列：基于阶乘的计数法。
 * 时间复杂度：O(N^2) (由于查找未使用数字是 O(N))
 */
char* getPermutation(int n, int k) {
    if (n == 0) {
        return strdup(""); // 返回空字符串
    }

    char *result = (char*)malloc(n + 1);
    if (result == NULL) {
        return NULL;
    }
    
    // 预计算阶乘和可用数字列表
    int factorials[10] = {1}; // 存储 0! 到 n! (n<=9)
    for (int i = 1; i <= n; i++) {
        factorials[i] = factorials[i - 1] * i;
    }

    // 可用数字列表。由于 C 无法高效移除元素，我们使用布尔数组来标记使用情况
    bool used[10] = {false}; 

    // 调整 k 为 0-based 索引
    k = k - 1; 
    
    for (int i = 0; i < n; i++) {
        // 当前位后面还有 (n - 1 - i) 个位置
        int fact_group = factorials[n - 1 - i];

        // 确定当前位是未使用的数字列表中的第几个 (0-based 索引)
        int index = k / fact_group;
        
        // 线性查找第 index 个未使用的数字 (O(N) 查找)
        int count = 0;
        int num_val = 0;
        for (int j = 1; j <= n; j++) {
            if (!used[j]) {
                if (count == index) {
                    num_val = j;
                    break;
                }
                count++;
            }
        }
        
        // 记录并标记已使用
        used[num_val] = true;
        result[i] = num_val + '0';

        // 更新 k：减去已经跳过的组的排列数
        k %= fact_group;
    }

    result[n] = '\0';
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 求解第 K 个排列：基于阶乘的计数法。
     * 时间复杂度：O(N^2) (由于 vector::erase 的开销)
     * 核心思想：确定每一位是哪个数字。
     */
    std::string getPermutation(int n, int k) {
        std::string result = "";
        
        // 1. 初始化数字列表和阶乘数组
        std::vector<int> numbers;
        std::vector<int> factorials(n + 1, 1);
        
        for (int i = 1; i <= n; ++i) {
            numbers.push_back(i);
            // 预计算阶乘：fact[i] = i!
            factorials[i] = factorials[i-1] * i;
        }

        // 调整 k 为 0-based 索引
        k--; 

        // 2. 从最高位开始确定数字
        for (int i = 1; i <= n; ++i) {
            // 当前位后面的 (n-i) 个位置，共有 (n-i)! 种排列组合。
            // 这一数字 fact_group 用来划分组。
            int fact_group = factorials[n - i];

            // 确定当前位是未使用的数字列表中的第几个 (0-based 索引)
            // index = k / fact_group
            int index = k / fact_group;
            
            // 将选定的数字添加到结果
            result += std::to_string(numbers[index]);
            
            // 从可用数字列表中移除该数字 (O(N) 操作)
            numbers.erase(numbers.begin() + index);

            // 更新 k：减去已经跳过的组的排列数
            k %= fact_group;
            
            // 优化：如果 k 已经为 0，且只剩一个数字，则可以提前结束
            if (k == 0 && i == n-1) {
                result += std::to_string(numbers[0]);
                break;
            }
        }

        return result;
    }
};
```

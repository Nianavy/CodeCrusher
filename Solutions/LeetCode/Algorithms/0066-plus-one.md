# Question

[plus-one](https://leetcode.cn/problems/plus-one/)



# Answer

> solution

### **解决思路总结：O(1) 额外空间的原地进位法**

**格言：** “不要为不需要进位的大多数情况浪费时间和内存。只有当整个结构坍塌时，才去重建它。”

**核心思想：**
将数组视为一个大整数。由于我们只进行“加 1”操作，最大的挑战是处理进位，以及当所有数字都是 9 时（例如 [9, 9, 9]），数组长度需要增加 1。我们采用从后向前遍历的策略，以最小化操作次数和额外空间。

1.  **从后向前遍历与原地修改：**
    *   从数组的最后一个元素开始向前遍历。
    *   **情况 A (无需进位)：** 如果当前数字小于 9，则将其加 1。此时，进位结束，我们已经找到了最终结果。立即停止循环，并返回（或复制）当前原地修改后的数组。
    *   **情况 B (需要进位)：** 如果当前数字是 9，则将其置为 0，并将进位传递给前一位。继续循环。

2.  **处理扩容的特殊情况：**
    *   如果循环结束（即 `i` 降到 -1）时，仍未遇到情况 A，这意味着原始数组中的所有数字都是 9（例如 [9, 9]）。
    *   **决策：** 此时，所有原始数字都已在循环中被置为 0。我们必须创建一个长度为 $N+1$ 的新数组。
    *   **构造：** 将新数组的第一个元素设置为 1，其余元素（已由默认初始化或 `memset` 保证）保持为 0。

3.  **最终返回：**
    *   绝大多数情况下，程序会在循环中提前返回原地修改后的数组（O(1) 额外空间）。
    *   仅在最坏的情况下（全 9 数组），才会分配并返回一个新的 O(N) 数组。

**实用主义的体现：**
该方法避免了对整个数组进行反转（两次 O(N) 操作）或使用额外的 O(N) 存储空间来保存中间结果。它在时间上达到了最优的 O(N)，并在空间上针对最常见的情况实现了 O(1) 的极致优化，是处理大数加法最简洁和高效的实现。

### Way Of C

> source code

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 * 采用 O(1) 额外空间的原地计算思想，但由于 C 函数签名要求，
 * 最终返回的数组必须是 malloced 的。
 */
int* plusOne(int* digits, int digitsSize, int* returnSize) {
    
    // 1. 尝试原地修改 (处理非全9情况)
    for (int i = digitsSize - 1; i >= 0; --i) {
        if (digits[i] < 9) {
            // 找到第一位非9数字，加1，并停止进位
            digits[i]++;
            
            // 复制结果到新的 malloced 数组中返回 (必须遵守函数签名)
            int *result = (int*)malloc(digitsSize * sizeof(int));
            if (result == NULL) {
                *returnSize = 0;
                return NULL;
            }
            memcpy(result, digits, digitsSize * sizeof(int));
            *returnSize = digitsSize;
            return result;
        }
        
        // 当前位是 9，置为 0，继续向前进位
        digits[i] = 0;
    }

    // 2. 特殊情况：所有位都是 9 (e.g., [9, 9] -> [1, 0, 0])
    // 此时 digits[] 全部为 0。
    
    // 返回数组长度增加 1
    *returnSize = digitsSize + 1;
    int *result = (int*)malloc(*returnSize * sizeof(int));
    if (result == NULL) {
        *returnSize = 0;
        return NULL;
    }
    
    // 最高位为 1
    result[0] = 1;
    // 其余位默认为 0 (如果使用 calloc 会更简洁，但这里保持明确)
    memset(result + 1, 0, digitsSize * sizeof(int));

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
     * 加一：原地修改 (O(1) 额外空间，除非需要增加长度)
     * 核心思想：从后向前遍历，处理进位。
     */
    std::vector<int> plusOne(std::vector<int>& digits) {
        int n = digits.size();

        // 1. 从末尾开始遍历
        for (int i = n - 1; i >= 0; --i) {
            if (digits[i] < 9) {
                // 如果当前位小于 9，直接加 1，结束进位，返回原地数组
                digits[i]++;
                return digits;
            }
            
            // 如果是 9，则置为 0，并继续向前进位
            digits[i] = 0;
        }

        // 2. 特殊情况：所有位都是 9 (e.g., [9, 9] -> [1, 0, 0])
        // 此时，上面的循环会结束，且所有 digits[i] 都已置为 0。
        // 我们需要一个新的数组，长度为 n + 1。
        std::vector<int> result(n + 1);
        result[0] = 1; // 最高位设为 1
        // 其余位默认为 0 (vector 初始化时已完成)
        
        return result;
    }
};
```

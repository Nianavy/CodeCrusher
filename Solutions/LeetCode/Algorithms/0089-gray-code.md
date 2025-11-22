# Question

[gray-code](https://leetcode.cn/problems/gray-code/)



# Answer

> solution

### 核心方法：数学公式法（$G(i) = i \oplus (i \gg 1)$）

格雷码的定义是：相邻的两个编码之间只有一位不同。数学家发现，第 $i$ 个二进制数 $i$ 与其右移一位后的结果进行**异或** ($\oplus$) 运算，可以直接得到第 $i$ 个格雷码 $G(i)$。

这种方法是最符合“好品味”的：它用**一步到位**的 $O(1)$ 位运算解决了问题，避免了多步迭代构造带来的复杂性。

#### 1. 确定总长度

首先确定要生成的格雷码序列的长度。对于 $n$ 位格雷码，总共有 $2^n$ 个码。

#### 2. 内存分配（或预分配）

无论是 C 语言的 `malloc` 还是 C++ 的 `std::vector` 预分配大小，我们都应该一次性分配好 $2^n$ 个整数的存储空间。这避免了在生成过程中因容量不足而导致的内存重分配和数据复制开销。

#### 3. 循环与赋值

从 $i=0$ 到 $2^n - 1$ 循环，直接使用公式 $G(i) = i \oplus (i \gg 1)$ 计算结果，并将其放入数组的第 $i$ 个位置。

### 复杂度分析

*   **时间复杂度：** $O(2^n)$。因为公式是 $O(1)$ 操作，我们只循环 $2^n$ 次。
*   **空间复杂度：** $O(2^n)$。用于存储最终结果。

### Way Of C

> source code

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* grayCode(int n, int* returnSize) {
    if (n < 0) {
        // 应该明确返回一个空列表而不是 NULL，但这取决于具体题目要求。
        // 在 C 语言中，返回 NULL 且 *returnSize = 0 是可以接受的。
        *returnSize = 0; 
        return NULL;
    }

    int count = 1 << n; // 总共 2^n 个格雷码
    int *codes = malloc(count * sizeof(int));
    if (!codes) {
        // 内存分配失败处理，虽然不常见，但必须考虑。
        *returnSize = 0;
        return NULL;
    }
    
    // 核心公式：G(i) = i ^ (i >> 1)
    for (int i = 0; i < count; i++) {
        codes[i] = i ^ (i >> 1);
    }

    *returnSize = count;
    return codes;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 公式 G(i) = i ^ (i >> 1)
     */
    vector<int> grayCode(int n) {
        if (n < 0) return {};
        
        int count = 1 << n;
        vector<int> res(count); 

        // 直接使用位移计算 count
        // 预先分配内存，只进行一次循环和赋值
        for (int i = 0; i < count; i++) {
            res[i] = i ^ (i >> 1);
        }
        
        return res;
    }
};
```

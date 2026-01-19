# Question

[factorial-trailing-zeroes](https://leetcode.cn/problems/factorial-trailing-zeroes/)



# Answer

> solution

**核心思路**
计算 `n!` 末尾零的个数，等价于计算其中因子 10 的个数。由于 10 = 2 × 5，且 `n!` 中因子 2 的个数远多于因子 5 的个数，问题简化为统计 `n!` 中因子 5 的总个数。

**实现方法**
通过迭代不断将 `n` 除以 5，每次累加商，直到 `n < 5`。这实际上在依次累加 `⌊n/5⌋ + ⌊n/25⌋ + ⌊n/125⌋ + ...`。

**最终方案**
使用循环直接计算上述级数的和，时间复杂度为 O(log₅ n)，空间复杂度为 O(1)，简洁高效。

### Way Of C

> source code

```c
int trailingZeroes(int n) {
    int count = 0;
    while (n > 0) {
        n /= 5;
        count += n;
    }
    return count;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int trailingZeroes(int n) {
        int count = 0;
        while (n > 0) {
            n /= 5;
            count += n;
        }
        return count;
    }
};
```

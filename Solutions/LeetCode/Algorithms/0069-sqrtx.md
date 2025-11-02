# Question

[sqrtx](https://leetcode.cn/problems/sqrtx/)



# Answer

> solution

### **解决思路总结：防溢出二分查找法**

**格言：** “数值计算中，你的代码必须能处理机器能容纳的最大值。溢出是不可原谅的 Bug。”

**核心思想：**
求解 $\lfloor \sqrt{x} \rfloor$ 本质上是一个搜索问题：在 $[0, x]$ 的范围内，找到最大的整数 `mid`，使得 $mid^2 \le x$。二分查找是最适合这种单调递增搜索的算法。

1.  **定义搜索范围：**
    *   搜索区间为 `[l, r]`，初始化 `l = 0` (或 1)，`r = x`。

2.  **解决溢出问题：**
    *   **乘法溢出 (Mid * Mid)：** 我们不能直接计算 $mid \times mid$ 并将其与 $x$ 比较，因为当 $mid$ 很大时，$mid^2$ 可能会超过标准整数类型的上限。
    *   **解决方案：** 转移判断条件，改用 $mid \le x / mid$。由于 $x$ 和 $mid$ 都是整数，整数除法 $x / mid$ 保证了比较的正确性，并避免了乘法溢出。
    *   **加法溢出 (L + R)：** 在计算中点 `mid = (l + r) / 2` 时，如果 $l$ 和 $r$ 都接近 $INT\_MAX$，它们的和可能溢出。
    *   **解决方案：** 在计算 $l + r$ 时，强制使用更大的整数类型（如 C/C++ 中的 `long long`）进行计算。

3.  **收敛策略（寻找最大满足条件的值）：**
    我们采用标准的二分查找模板来寻找满足条件的最大值，并确保循环不会陷入死循环：
    *   计算中点时，采用向上取整：`mid = (l + r + 1) / 2`。这保证了当 `l = r - 1` 时，`mid` 总是等于 `r`，避免了 `l = mid` 时的死循环。
    *   **如果 $mid^2 \le x$**：`mid` 可能是解，我们尝试在 `[mid, r]` 之间寻找更大的解，设置 `l = mid`。
    *   **如果 $mid^2 > x$**：`mid` 太大，解在 `[l, mid - 1]` 之间，设置 `r = mid - 1`。

4.  **结果：**
    *   循环终止时，`l` 等于 `r`，这个值就是 $\lfloor \sqrt{x} \rfloor$。

**实用主义的体现：**
该方法通过二分查找达到了最优的时间复杂度 O($\log x$)，并且通过精确的防溢出判断，确保了在处理整个 32 位整数范围时的数值准确性和代码的健壮性。这是数值算法和二分查找应用结合的最佳范例。

### Way Of C

> source code

```c
/**
 * 计算整数平方根 (取整)
 * 使用二分查找：寻找最大的整数 mid，使得 mid * mid <= x。
 * 时间复杂度：O(log x)
 */
int mySqrt(int x) {
    if (x < 0) return 0;
    if (x == 0 || x == 1) return x;

    // 边界 [0, x]
    int l = 1;
    int r = x;
    
    // l 最终将收敛到满足条件的最大整数解
    while (l < r) {
        // 使用 64位长整型 (long long) 来计算 mid，防止 l+r 溢出
        // 向上取整：(l + 1LL + r) / 2
        long long mid_ll = (long long)l + 1LL + r;
        mid_ll /= 2;
        int mid = (int)mid_ll;
        
        // 核心判断：mid * mid <= x，但使用 mid <= x / mid 避免 mid * mid 溢出
        // 注意：x / mid 必须是整数运算
        if (mid <= x / mid) {
            // mid^2 <= x，mid 可能是解，尝试更大的 mid
            l = mid;
        } else {
            // mid^2 > x，mid 太大，减小上限
            r = mid - 1;
        }
    }

    // 循环结束时，l 就是结果
    return l;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 计算整数平方根 (取整)
     * 使用二分查找：寻找最大的整数 mid，使得 mid * mid <= x。
     * 时间复杂度：O(log x)
     */
    int mySqrt(int x) {
        if (x < 0) return 0; // 题目通常保证 x >= 0

        // 边界 [0, x]
        // 由于结果是整数，我们可以在 [0, x] 范围内查找
        int l = 0;
        // 使用 x 而不是 x/2 是因为 x=1 或 x=2 时，x/2=0 会导致 r=0，但解是 1
        int r = x; 

        // l 最终将收敛到满足条件的最大整数解
        while (l < r) {
            // 计算中点 mid，使用 1LL 防止 l+r 溢出，并向上取整。
            // 向上取整是为了防止 l = mid 时陷入死循环 (l = mid = l + r / 2)
            // mid = l + (r - l) / 2; // 下取整，用于 r = mid 的场景
            long long mid = l + 1LL + r;
            mid >>= 1; // 等同于 mid / 2

            // 判断条件：mid <= x / mid 避免 mid * mid 溢出
            if (mid <= x / mid) {
                // mid^2 <= x，mid 可能是解，尝试更大的 mid
                l = (int)mid;
            } else {
                // mid^2 > x，mid 太大，减小上限
                r = (int)mid - 1;
            }
        }

        // 循环结束时，l == r，l 就是最大的满足 mid^2 <= x 的整数
        return l;
    }
};
```

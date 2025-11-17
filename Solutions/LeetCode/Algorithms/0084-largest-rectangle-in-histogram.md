# Question

[largest-rectangle-in-histogram](https://leetcode.cn/problems/largest-rectangle-in-histogram/)



# Answer

> solution

柱状图中最大的矩形

这个问题要求找到 $O(N)$ 时间复杂度的解法，而其核心在于确定每个柱子能扩展到的最大宽度，即找到它的**左边第一个更矮的柱子**和**右边第一个更矮的柱子**。

#### 核心哲学：单调性是解决 O(N) 问题的利器。

1.  **问题分解：**
    *   对于任何给定的柱子 $h_i$，它能形成的最大矩形高度就是 $h_i$ 本身。
    *   关键是找到宽度：左侧边界 $L$ 和右侧边界 $R$，使得 $h_L < h_i$ 且 $h_R < h_i$，并且 $L$ 和 $R$ 是离 $i$ 最近的。最大宽度就是 $R - L - 1$。
    *   最大面积 = $\max(h_i \times (R_i - L_i - 1))$。

2.  **单调栈的作用 (Monotonic Stack)：**
    *   单调栈是解决“寻找左边/右边第一个更大/更小元素”这类问题的标准工具。
    *   **策略：** 维护一个**单调递增栈**（存储索引）。当遇到一个新柱子 $h_i$，如果 $h_i$ 比栈顶的柱子矮，则栈顶柱子找到了它的**右边界**（即 $i$）。
    *   **效率：** 每个索引只入栈和出栈一次，保证了整体复杂度为 $O(N)$。

3.  **两种实现方式的取舍：**

    *   **双遍历 (Two Passes)：**
        *   **优势：** 逻辑清晰。第一次遍历计算所有 $L_i$，第二次遍历计算所有 $R_i$。
        *   **品味缺陷：** 存在代码重复。
        *   **C 语言致命缺陷：** 使用 `malloc` 必须伴随 `free`。你的原始 C 代码没有 `free`，这是**不可容忍的资源泄漏**。

    *   **单遍历 + 哨兵 (One Pass + Sentinels)：**
        *   **优势：** **最优品味。** 通过在数组头尾添加高度为 0 的哨兵，消除了所有的边界条件判断（`stk.empty()` 或 `i == n`）。代码最为紧凑和优雅。

4.  **实用主义总结：**
    *   **C++ 推荐：** 单遍历 + 哨兵方案，它通过数据结构解决了算法的边界问题。
    *   **C 语言要求：** 无论选择何种遍历方案，必须在函数退出前调用 `free()` 释放所有通过 `malloc()` 分配的内存。这是 C 语言中的**资源管理铁律**。

### Way Of C

> source code

```c
/* Helper function for max */
static int max(int a, int b) {
    return a > b ? a : b;
}

int largestRectangleArea(int* heights, int heightsSize)
{
    // 必须分配内存
    int *idx_stk = (int*)malloc(heightsSize * sizeof(int));
    int *lmax = (int*)malloc(heightsSize * sizeof(int));
    int *rmax = (int*)malloc(heightsSize * sizeof(int));

    // 检查分配是否成功，这是 C 语言的防御性编程
    if (!idx_stk || !lmax || !rmax) {
        // 实际生产中需要更复杂的错误处理，这里简化
        if (idx_stk) free(idx_stk);
        if (lmax) free(lmax);
        if (rmax) free(rmax);
        return 0; 
    }

    int i, pos = 0;
    
    // 第一次遍历：查找左边界 (lmax)
    for (i = 0; i < heightsSize; i++) {
        /* 保持单调递增栈 */
        while (pos > 0 && heights[idx_stk[pos - 1]] >= heights[i]) {
            pos--;
        }
        // 左边界：栈为空则为 -1，否则为栈顶索引
        lmax[i] = pos == 0 ? -1 : idx_stk[pos - 1];
        idx_stk[pos++] = i;
    }

    pos = 0; // 重置栈顶指针，复用 idx_stk 数组
    
    // 第二次遍历：查找右边界 (rmax)
    for (i = heightsSize - 1; i >= 0; i--) {
        /* 保持单调递增栈 */
        while (pos > 0 && heights[idx_stk[pos - 1]] >= heights[i]) {
            pos--;
        }
        // 右边界：栈为空则为 heightsSize，否则为栈顶索引
        rmax[i] = pos == 0 ? heightsSize : idx_stk[pos - 1];
        idx_stk[pos++] = i;
    }

    int max_area = 0;
    
    // 计算最大面积
    for (i = 0; i < heightsSize; i++) {
        int area = heights[i] * (rmax[i] - lmax[i] - 1);
        max_area = max(max_area, area);
    }

    // 致命修正：释放所有分配的内存
    free(idx_stk);
    free(lmax);
    free(rmax);

    return max_area;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n = heights.size();
        if (n == 0) {
            return 0;
        }

        // 使用哨兵 (Sentinel) 消除边界特殊情况：
        // 在两端添加高度为 0 的柱子，确保所有柱子都能入栈和出栈。
        vector<int> h(n + 2, 0); 
        for (int i = 0; i < n; ++i) {
            h[i + 1] = heights[i];
        }

        stack<int> stk; // 存储柱子索引
        int max_area = 0;

        for (int i = 0; i < n + 2; ++i) {
            // 当找到一个比栈顶柱子矮的柱子 h[i] 时，栈顶柱子找到了它的右边界 i。
            while (!stk.empty() && h[stk.top()] > h[i]) {
                int height_idx = stk.top();
                stk.pop();
                
                // height 是当前出栈柱子的高度。
                int height = h[height_idx];
                
                // width 的右边界是 i，左边界是新的栈顶元素（stk.top()）。
                // 注意：由于我们加了哨兵，stk.top() 永远不会为空。
                int width = i - stk.top() - 1;
                
                max_area = max(max_area, height * width);
            }
            // 将当前柱子索引入栈。
            stk.push(i);
        }

        return max_area;
    }
};
```

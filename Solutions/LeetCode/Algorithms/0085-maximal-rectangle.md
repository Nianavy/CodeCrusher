# Question

[maximal-rectangle](https://leetcode.cn/problems/maximal-rectangle/)



# Answer

> solution

最大矩形 (Maximal Rectangle)

解决这个 2D 问题的思路是**将复杂性降维**，并使用最有效率的 1D 算法来解决每一步的子问题。

#### 核心哲学：降维，以及对资源管理的执念。

1.  **降维转换 (2D -> 1D)：**
    *   **原理：** 最大矩形一定由某一行作为其底边。
    *   **策略：** 逐行遍历矩阵。对于每一行 $i$，我们计算一个一维数组 `heights`，其中 `heights[j]` 代表从当前行向上看，连续 '1' 构成的柱子高度。如果 `matrix[i][j]` 是 '0'，则 `heights[j]` 归零。
    *   **时间/空间：** 这个转换过程是 $O(N \times M)$ 时间，并且只需要 $O(M)$ 空间（存储当前的 `heights` 数组）。

2.  **子问题解决 (1D 柱状图)：**
    *   **子问题：** 针对每一行生成的 `heights` 数组，求解“柱状图中最大的矩形”。
    *   **算法：** 必须使用 $O(M)$ 的**单调栈**算法。
    *   **品味选择：** 采用**单次遍历 + 哨兵**的单调栈实现，因为它是最简洁、最能消除边界特殊情况的方案。

3.  **内存管理：实用主义的铁律**
    *   这是 C 语言代码中最主要的缺陷。任何通过 `malloc` 分配的资源，都必须通过 `free` 释放。
    *   **缺陷分析：** 原始提交在 **外层函数** (`maximalRectangle`) 和 **内层函数** (`area_calc`) 中都有 `malloc`，但都没有对应的 `free`，造成双重泄漏。
    *   **修正：** 必须确保所有分配 (`heights`, `idx_stk`, `lmax`, `rmax`) 在函数返回前都被释放。

**最终思路：**
使用 $O(M)$ 空间高效地维护一个 `heights` 数组，在 $O(N)$ 次迭代中，将 2D 矩阵问题转化为 $N$ 个独立的 $O(M)$ 单调栈问题，并确保过程中所有动态分配的资源都被妥善清理。

### Way Of C

> source code

```c
static inline int max(int a, int b)
{
    return a > b ? a : b;
}

static int _area_calc(int *heights, int size)
{
    int *idx_stk = (int*)malloc(size * sizeof(int));
    int *lmax = (int*)malloc(size * sizeof(int));
    int *rmax = (int*)malloc(size * sizeof(int));
    
    if (!idx_stk || !lmax || !rmax) {
        // 资源清理
        if (idx_stk) free(idx_stk);
        if (lmax) free(lmax);
        if (rmax) free(rmax);
        return 0;
    }

    int i, pos = 0;
    
    // 查找左边界
    for (i = 0; i < size; i++) {
        while (pos > 0 && heights[idx_stk[pos - 1]] >= heights[i]) {
            pos--;
        }
        lmax[i] = pos == 0 ? -1 : idx_stk[pos - 1];
        idx_stk[pos++] = i;
    }

    pos = 0; // 重置栈顶指针，复用 idx_stk
    
    // 查找右边界
    for (i = size - 1; i >= 0; i--) {
        while (pos > 0 && heights[idx_stk[pos - 1]] >= heights[i]) {
            pos--;
        }
        rmax[i] = pos == 0 ? size : idx_stk[pos - 1];
        idx_stk[pos++] = i;
    }

    int max_area = 0;
    for (i = 0; i < size; i++) {
        int area = heights[i] * (rmax[i] - lmax[i] - 1);
        max_area = max(area, max_area);
    }

    free(idx_stk);
    free(lmax);
    free(rmax);

    return max_area;
}


int maximalRectangle(char** matrix, int matrixSize, int* matrixColSize)
{
    if (matrixSize == 0 || matrixColSize[0] == 0) {
        return 0;
    }

    int i, j;
    int cols = matrixColSize[0];
    int max_area = 0;
    
    int *heights = (int*)malloc(cols * sizeof(int));
    if (!heights) return 0;

    memset(heights, 0, cols * sizeof(int));

    for (i = 0; i < matrixSize; i++) {
        for (j = 0; j < cols; j++) {
            // 更新当前行的柱状图高度
            heights[j] = matrix[i][j] == '1' ? heights[j] + 1 : 0;
        }
        // 计算当前柱状图的最大矩形面积
        max_area = max(max_area, _area_calc(heights, cols));
    }
    
    free(heights);

    return max_area;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
private:
    // 使用单次遍历+哨兵的 O(M) 方案
    int largestRectangleArea(const vector<int>& heights) {
        int n = heights.size();
        if (n == 0) return 0;
        
        // 1. 添加哨兵：头尾添加 0 高度，消除边界检查。
        vector<int> h(n + 2, 0); 
        for (int i = 0; i < n; ++i) {
            h[i + 1] = heights[i];
        }
        
        stack<int> stk; // 存储索引
        int max_area = 0;
        
        for (int i = 0; i < n + 2; ++i) {
            // 当 h[i] 比栈顶矮时，栈顶柱子找到了右边界 i。
            while (!stk.empty() && h[stk.top()] > h[i]) {
                int height_idx = stk.top();
                stk.pop();
                
                int height = h[height_idx];
                
                // 左边界是新的栈顶元素。
                // 宽度 = 右边界 i - 左边界 (stk.top()) - 1
                int width = i - stk.top() - 1;
                
                max_area = max(max_area, height * width);
            }
            stk.push(i);
        }
        return max_area;
    }

public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        
        int rows = matrix.size();
        int cols = matrix[0].size();
        
        // O(M) 空间：只存储当前行的柱高。
        vector<int> heights(cols, 0);
        int max_area = 0;

        for (int i = 0; i < rows; i++) {
            // 1. 更新柱状图高度：将 2D 降维为 1D
            for (int j = 0; j < cols; j++) {
                if (matrix[i][j] == '1') {
                    heights[j]++;
                } else {
                    heights[j] = 0;
                }
            }
            
            // 2. 解决 1D 问题，并更新最大面积。
            max_area = max(max_area, largestRectangleArea(heights));
        }

        return max_area;
    }
};
```

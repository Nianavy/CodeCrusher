# Question

[spiral-matrix-ii](https://leetcode.cn/problems/spiral-matrix-ii/)



# Answer

> solution

### **解决思路总结：边界收缩填充法**

**格言：** “不要跟踪每个坐标点。只要控制好边界，路径自然会形成。”

**核心思想：**
生成螺旋矩阵本质上是按照固定顺序（右、下、左、上）对一个不断收缩的矩形区域进行填充。我们通过维护四个边界来定义当前待填充的矩形区域，每填充完一个方向，就将该方向的边界收缩一位，直到区域完全消失。

1.  **定义边界：**
    *   `top` (上边界)、`bottom` (下边界)、`left` (左边界)、`right` (右边界)。
    *   初始化时，它们定义了整个 $N \times N$ 矩阵的范围。

2.  **循环条件与终止：**
    *   使用 `while (top <= bottom && left <= right)` 作为主循环条件。只要这两个条件同时成立，就说明矩阵中还有未填充的区域。
    *   **关键优化：** 在每个方向的填充结束后，我们**必须立即收缩相应的边界**（例如，填充完顶部后 `top++`），然后**立即检查**主循环条件是否仍然成立。这确保了在处理奇数维矩阵的中心点或单行/单列退化矩阵时，不会发生重复填充或访问越界。

3.  **四步填充与边界收缩：**
    在循环内部，我们按照固定的右-下-左-上顺序，每完成一步就收缩边界：

    *   **右 (Right)：** 填充 `top` 行，从 `left` 到 `right`。完成后，`top++`。
    *   **下 (Down)：** 填充 `right` 列，从 `top` 到 `bottom`。完成后，`right--`。
    *   **左 (Left)：** 填充 `bottom` 行，从 `right` 到 `left`（倒序）。完成后，`bottom--`。
    *   **上 (Up)：** 填充 `left` 列，从 `bottom` 到 `top`（倒序）。完成后，`left++`。

4.  **数值管理：**
    *   使用一个单独的计数器 `num`（从 1 开始），每次填充一个单元格后，`num` 递增，确保按顺序填充。

**实用主义的体现：**
边界收缩法以其结构化和清晰的流程，成为了螺旋填充问题的标准解法。它将复杂的二维路径问题转化为简单的四个一维循环和四个边界变量的管理，消除了使用方向数组和已访问标记带来的额外逻辑负担和内存开销。这种方法在逻辑上是最健壮、最优雅的。

### Way Of C

> source code

```c
/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *returnColumnSizes array must be malloced, assume caller calls free().
 */
int** generateMatrix(int n, int* returnSize, int** returnColumnSizes)
{
    if (n == 0) {
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }
    
    // 分配 N*N 元素的连续内存块
    int *data = (int*)malloc(n * n * sizeof(int));
    // 分配 N 个指针的数组
    int **matrix = (int**)malloc(n * sizeof(int*));
    
    if (data == NULL || matrix == NULL) {
        // 内存分配失败处理
        free(data);
        free(matrix);
        *returnSize = 0;
        *returnColumnSizes = NULL;
        return NULL;
    }

    // 设置指针，使 matrix[i] 指向 data 中对应的行
    for (int i = 0; i < n; i++) {
        matrix[i] = &data[i * n];
    }
    
    // 设置返回参数
    *returnSize = n;
    *returnColumnSizes = (int*)malloc(n * sizeof(int));
    for (int i = 0; i < n; i++) {
        (*returnColumnSizes)[i] = n;
    }

    // 边界收缩和填充逻辑
    int top = 0;
    int bottom = n - 1;
    int left = 0;
    int right = n - 1;
    int num = 1;

    while (top <= bottom && left <= right) {
        
        // 1. 从左到右
        for (int col = left; col <= right; ++col) {
            matrix[top][col] = num++;
        }
        top++;
        if (top > bottom) break;

        // 2. 从上到下
        for (int row = top; row <= bottom; ++row) {
            matrix[row][right] = num++;
        }
        right--;
        if (left > right) break;

        // 3. 从右到左
        for (int col = right; col >= left; --col) {
            matrix[bottom][col] = num++;
        }
        bottom--;
        if (top > bottom) break;
        
        // 4. 从下到上
        for (int row = bottom; row >= top; --row) {
            matrix[row][left] = num++;
        }
        left++;
    }

    // 注意：data 的内存块通过 matrix 指针数组访问。
    // 释放时需要先释放 matrix，然后释放 data。调用者负责释放。
    return matrix;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 螺旋矩阵生成：使用边界收缩法。
     * 时间复杂度：O(N^2)
     */
    std::vector<std::vector<int>> generateMatrix(int n) {
        if (n == 0) {
            return {};
        }

        std::vector<std::vector<int>> matrix(n, std::vector<int>(n));
        
        // 四个边界
        int top = 0;
        int bottom = n - 1;
        int left = 0;
        int right = n - 1;
        
        int num = 1; // 从 1 开始填充

        // 只要还有未填充的区域
        while (top <= bottom && left <= right) {
            
            // 1. 从左到右 (Top Row)
            for (int col = left; col <= right; ++col) {
                matrix[top][col] = num++;
            }
            top++;

            // 检查边界是否交叉
            if (top > bottom) break;

            // 2. 从上到下 (Right Column)
            for (int row = top; row <= bottom; ++row) {
                matrix[row][right] = num++;
            }
            right--;

            // 检查边界是否交叉
            if (left > right) break;

            // 3. 从右到左 (Bottom Row)
            for (int col = right; col >= left; --col) {
                matrix[bottom][col] = num++;
            }
            bottom--;

            // 检查边界是否交叉
            if (top > bottom) break;
            
            // 4. 从下到上 (Left Column)
            for (int row = bottom; row >= top; --row) {
                matrix[row][left] = num++;
            }
            left++;
        }

        return matrix;
    }
};
```

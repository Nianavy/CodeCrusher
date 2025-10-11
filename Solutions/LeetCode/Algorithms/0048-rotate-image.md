# Question

[rotate-image](https://leetcode.cn/problems/rotate-image/)



# Answer

> solution

解决“旋转图像”问题的最佳实践是：**将复杂的 90 度旋转分解为两次更简单的翻转操作，或者直接进行四点循环交换。两种方法都能在 O(1) 额外空间内完成。**

#### **方案一：两次翻转法 (Transpose then Reverse Rows)**

这是最简洁、最优雅且通常推荐的方法，因为它将一个复杂的旋转操作分解为两个标准的矩阵操作。

1.  **第一步永远是简化数据结构。**
    *   **思想：** 直接在原矩阵上操作，不创建任何大型辅助结构。
    *   **实现：** 仅使用少量的临时变量（如 `tmp`）和循环指针。

2.  **消除所有特殊情况，将它们融入通用逻辑。**
    *   **边界条件：** 对于空矩阵或 1x1 矩阵，无需旋转，直接返回。
    *   **通用性：** 两次翻转的逻辑适用于任何 `N x N` 矩阵，无需特殊处理不同的 `N` 值。

3.  **用最直接、最清晰（即使有时看起来“笨拙”）的方式实现。**
    *   **第一步：沿主对角线翻转 (Transpose)**
        *   **操作：** 将 `matrix[i][j]` 与 `matrix[j][i]` 交换。
        *   **遍历范围：** 只需要遍历矩阵的**上三角部分**（`i` 从 `0` 到 `N-1`，`j` 从 `0` 到 `i-1`）。这样做可以避免重复交换，并且不会越界。
        *   **效果：** 这一步将 `(row, col)` 元素移动到 `(col, row)`。
    *   **第二步：沿水平中线翻转 (Reverse Each Row)**
        *   **操作：** 对矩阵的每一行进行反转操作。将 `matrix[i][j]` 与 `matrix[i][N - 1 - j]` 交换。
        *   **遍历范围：** `i` 从 `0` 到 `N-1`，`j` 从 `0` 到 `N/2 - 1`（或使用双指针 `j` 从 `0`，`k` 从 `N-1` 向中间靠拢，`j < k`）。
        *   **效果：** 这一步将 `(col, row)` 元素移动到 `(col, N - 1 - row)`。
    *   **组合效果：** 两次操作组合起来，` (row, col) -> (col, row) -> (col, N - 1 - row) `，正是顺时针旋转 90 度的映射关系。

4.  **确保零破坏性，向后兼容是最高优先级。**
    *   该算法直接在 `matrix` 上进行原地修改，不返回新的矩阵。这符合题目对原地操作的要求。

#### **方案二：四点循环交换法 (Cycle Swaps)**

这种方法直接处理旋转的四个点，概念上是直观的映射。

1.  **思想：** 将矩阵想象成由多个同心正方形“层”组成。每次迭代处理一层，每层中的每个元素及其旋转后的三个对应位置形成一个四元组，进行循环交换。

2.  **实现：**
    *   **外层循环：** `for (int i = 0; i < N / 2; ++i)`，`i` 表示当前处理的正方形层的索引（从最外层 0 到最内层 `N/2 - 1`）。
    *   **内层循环：** `for (int j = i; j < N - 1 - i; ++j)`，`j` 表示在当前层 `i` 中，从左上角开始的偏移量。这个循环范围确保只处理当前层的非重复元素。
    *   **四点交换：** 对于当前 `(i, j)` 位置的元素，其顺时针旋转 90 度的路径是：
        *   `matrix[i][j]` (top-left) -> `matrix[j][N - 1 - i]` (top-right)
        *   `matrix[j][N - 1 - i]` (top-right) -> `matrix[N - 1 - i][N - 1 - j]` (bottom-right)
        *   `matrix[N - 1 - i][N - 1 - j]` (bottom-right) -> `matrix[N - 1 - j][i]` (bottom-left)
        *   `matrix[N - 1 - j][i]` (bottom-left) -> `matrix[i][j]` (top-left)
        通过一个临时变量 `tmp`，将这四个值循环赋值。

**总结来说，两种方法都实现了 O(N^2) 时间和 O(1) 空间的最佳实践。两次翻转法通常在代码上更简洁，因为它利用了两个已知的矩阵操作；而四点循环交换法则直接映射了旋转路径，需要更精确的坐标计算。选择哪种取决于个人对代码风格和直观性的偏好。但 C 语言版本无论如何都要确保正确的内存管理。**

### Way Of C

> source code

```c
// 辅助函数：交换两个整数的值
static inline void swap_int(int *a, int *b)
{
    int tmp = *a;
    *a = *b;
    *b = tmp;
}

// matrixColSize 在这里不再需要
// (因为是方阵，所有列大小都等于 matrixSize)
void rotate(int** matrix, int matrixSize, int* matrixColSize /* 实际未使用 */)
{
    // 边界条件：空矩阵或 1x1 矩阵，无需旋转
    if (matrixSize <= 1) {
        return;
    }

    int N = matrixSize;

    // 1. 第一次翻转：沿主对角线翻转 (Transpose)
    // 对于 matrix[i][j] 的元素，将其与 matrix[j][i] 交换。
    // 只需遍历上三角部分，避免重复交换。
    for (int i = 0; i < N; i++) {
        for (int j = 0; j < i; j++) { // j < i 确保只处理上三角
            swap_int(&matrix[i][j], &matrix[j][i]);
        }
    }

    // 2. 第二次翻转：沿水平中线翻转 (Reverse each row)
    // 对于每一行，将该行左右对称的元素进行交换。
    for (int i = 0; i < N; i++) {
        // 使用双指针 j 和 k 从两端向中间移动
        for (int j = 0, k = N - 1; j < k; j++, k--) {
            swap_int(&matrix[i][j], &matrix[i][k]);
        }
    }
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    void rotate(std::vector<std::vector<int>>& matrix) {
        int N = matrix.size();

        // 边界条件：空矩阵或 1x1 矩阵，无需旋转
        if (N <= 1) {
            return;
        }

        // 1. 第一次翻转：沿主对角线翻转 (Transpose)
        // 对于 matrix[i][j] 的元素，将其与 matrix[j][i] 交换。
        // 只需遍历上三角部分 (j < i)，避免重复交换。
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < i; ++j) {
                std::swap(matrix[i][j], matrix[j][i]);
            }
        }

        // 2. 第二次翻转：沿水平中线翻转 (Reverse each row)
        // 对于每一行，将该行左右对称的元素进行交换。
        // 可以使用 std::reverse(row.begin(), row.end());
        // 或者手动双指针交换。
        for (int i = 0; i < N; ++i) {
            // std::reverse(matrix[i].begin(), matrix[i].end()); // 更简洁
            for (int j = 0, k = N - 1; j < k; ++j, --k) {
                std::swap(matrix[i][j], matrix[i][k]);
            }
        }
    }
};
```

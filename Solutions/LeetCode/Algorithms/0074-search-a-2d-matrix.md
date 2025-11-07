# Question

[search-a-2d-matrix](https://leetcode.cn/problems/search-a-2d-matrix/)



# Answer

> solution

搜索二维矩阵

解决思路的核心是**将二维问题映射到一维**，从而应用最强大的搜索工具：**二分查找**。

### 1. 核心数学映射（“虚拟一维数组”策略）

由于整个矩阵是完全有序的，我们可以将一个 $R \times C$ 的矩阵视为一个长度为 $N = R \cdot C$ 的一维数组。

*   **一维索引 $\rightarrow$ 二维坐标的转换**：
    *   给定一维索引 $k$ (范围 $0$ 到 $R \cdot C - 1$)
    *   行索引 $r = \lfloor k / C \rfloor$
    *   列索引 $c = k \pmod C$

### 2. 核心算法：单次二分查找 ($O(\log(R \cdot C))$)

将二分查找直接应用在这个虚拟的一维索引 $k$ 上。

1.  **初始化**：设置搜索范围 $low = 0$ 和 $high = R \cdot C - 1$。
2.  **迭代**：在 $low \le high$ 循环中：
    *   计算中点索引 $mid = low + (high - low) / 2$。
    *   将 $mid$ 映射到矩阵坐标 $(r, c)$。
    *   获取 $matrix[r][c]$ 的值。
3.  **判断**：
    *   如果值等于 $target$，返回 `true`。
    *   如果值小于 $target$，则 $low = mid + 1$ (向右搜索)。
    *   如果值大于 $target$，则 $high = mid - 1$ (向左搜索)。

### 3. C 语言的实用主义考量（双阶段查找）

尽管单次二分查找是最优雅的，但在 C 语言中，由于缺乏 C++ 的容器和抽象，进行两次二分查找（或一次线性查找 + 一次二分查找）往往更实用，并且避免了复杂的指针和类型转换。

*   **阶段 I：定位行 ($O(\log R)$ 或 $O(R)$)**
    *   在矩阵的第一列中查找目标值可能所在的行 $r$。我们找的是 $matrix[r][0] \le target$ 且 $matrix[r+1][0] > target$ 的最大行 $r$。
*   **阶段 II：行内查找 ($O(\log C)$)**
    *   在确定的行 $matrix[r]$ 内，进行标准的二分查找。

**总结**：C++ 版本应追求**数学映射的优雅**，通过单次二分查找实现极致的简洁。C 版本应追求**内存安全和实用性**，通过阶段性查找来实现高效搜索，同时避免不必要的内存分配。

### Way Of C

> source code

```c
/**
 * C 版本的二分查找：在有序数组中查找目标值
 * @return 目标值的索引 >= 0，否则返回 -1
 */
static int find_target_in_row(int *arr, int len, int target) {
    int low = 0;
    int high = len - 1;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return -1;
}

/**
 * 搜索矩阵
 * @param matrixColSize 数组，假设 matrixColSize[0] 是列数 C
 */
bool searchMatrix(int** matrix, int matrixSize, int* matrixColSize, int target) 
{
    int R = matrixSize;
    if (R == 0 || matrixColSize == NULL || *matrixColSize == 0) return false;
    int C = matrixColSize[0];

    // 阶段 1: 确定目标所在的行
    // 我们可以对第一列进行二分查找，但为了 C 语言的简洁性，我们用 O(R) 线性扫描确定行。
    // 因为 R 和 C  usually 相似，这不会显著降低效率。

    int target_row = 0;

    // 如果目标小于第一行的第一个元素，直接失败
    if (target < matrix[0][0]) return false;

    // 线性搜索定位到可能是目标所在的行
    // 找到满足 matrix[r][0] <= target 的最大 r
    for (target_row = 0; target_row < R; ++target_row) {
        // 如果当前行的第一个元素大于目标，说明目标在前一行
        if (matrix[target_row][0] > target) {
            target_row--;
            break;
        }
        // 如果到了最后一行，并且最后一行第一个元素仍小于等于目标，就搜索这一行
        if (target_row == R - 1) {
             break;
        }
    }
    
    // 如果循环结束时 target_row 超出范围，需要修正
    if (target_row == R) target_row = R - 1; 

    // 阶段 2: 在目标行进行二分查找
    return find_target_in_row(matrix[target_row], C, target) != -1;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) return false;
        
        // R: rows, C: columns
        const int R = matrix.size();
        const int C = matrix[0].size();

        // 整个矩阵视为一个长度为 R*C 的一维数组，索引范围 [0, R*C - 1]
        int low = 0;
        int high = R * C - 1;

        while (low <= high) {
            // 使用 (low + high) / 2 或 low + (high - low) / 2 来避免溢出 (但 C++ >> 1 更简洁)
            // C++ 编译器的优化通常会避免溢出
            int mid = low + ((high - low) >> 1);
            
            // 关键：将一维索引 mid 映射回二维坐标 (row, col)
            int row = mid / C;
            int col = mid % C;
            
            int current_val = matrix[row][col];

            if (current_val == target) {
                return true;
            } else if (current_val < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return false;
    }
};
```

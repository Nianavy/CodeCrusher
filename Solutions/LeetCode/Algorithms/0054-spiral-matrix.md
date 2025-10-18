# Question

[spiral-matrix](https://leetcode.cn/problems/spiral-matrix/)



# Answer

> solution

### **解决思路总结：边界收缩法**

**格言：** “不要去跟踪走过的每一步，只需要知道你目前在哪里，以及还有多少地方没走。”

**核心思想：**
螺旋矩阵遍历问题，本质上是从外向内一层一层地剥洋葱。我们可以通过维护四个边界（上、下、左、右）来精确地定义当前需要遍历的“一层”。每遍历完一个方向，就收缩相应的边界，然后转向下一个方向，直到边界相遇或交叉，表示所有元素已遍历完毕。

1.  **定义边界：**
    *   `top`：当前遍历区域的最上行索引。
    *   `bottom`：当前遍历区域的最下行索引。
    *   `left`：当前遍历区域的最左列索引。
    *   `right`：当前遍历区域的最右列索引。
    *   初始化时，`top = 0`, `bottom = rows - 1`, `left = 0`, `right = cols - 1`。

2.  **循环条件：**
    *   只要 `top <= bottom` 且 `left <= right`，就说明当前区域仍然有效，还有元素可以遍历。当这两个条件之一不再满足时，表示所有元素已遍历或区域已退化为无效。

3.  **遍历方向与边界收缩：**
    在一个 `while` 循环内部，依次进行四个方向的遍历，每个方向遍历结束后，收缩相应的边界：

    *   **从左到右 (遍历上边界)：**
        *   遍历 `matrix[top][col]`，其中 `col` 从 `left` 到 `right`。
        *   遍历完成后，`top` 边界下移一位 (`top++`)。
        *   **立即检查**：如果此时 `top > bottom`，说明所有行都已遍历完（例如单行矩阵），提前退出循环。

    *   **从上到下 (遍历右边界)：**
        *   遍历 `matrix[row][right]`，其中 `row` 从 `top` 到 `bottom`。
        *   遍历完成后，`right` 边界左移一位 (`right--`)。
        *   **立即检查**：如果此时 `left > right`，说明所有列都已遍历完（例如单列矩阵），提前退出循环。

    *   **从右到左 (遍历下边界)：**
        *   遍历 `matrix[bottom][col]`，其中 `col` 从 `right` 到 `left`。
        *   遍历完成后，`bottom` 边界上移一位 (`bottom--`)。
        *   **立即检查**：如果此时 `top > bottom`，提前退出循环。

    *   **从下到上 (遍历左边界)：**
        *   遍历 `matrix[row][left]`，其中 `row` 从 `bottom` 到 `top`。
        *   遍历完成后，`left` 边界右移一位 (`left++`)。
        *   **立即检查**：如果此时 `left > right`，提前退出循环。

4.  **结果：**
    *   每次遍历到的元素都按顺序添加到结果数组中。
    *   循环结束后，结果数组包含了所有元素的螺旋顺序。

**简洁执念的体现：**
这种方法避免了使用额外的 `visited` 矩阵（这会增加空间开销和复杂的越界/访问判断），而是通过巧妙地调整四个边界，将复杂的路径跟踪转化为简单的范围控制。每个方向的遍历和边界收缩都是原子操作，逻辑清晰，没有冗余或不必要的判断。尤其是在每次边界收缩后立即检查 `top <= bottom && left <= right` 的条件，确保了程序的鲁棒性，避免了在单行/单列等极端情况下访问已越界或重复的区域，体现了“好品味”。

### Way Of C

> source code

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* spiralOrder(int** matrix, int matrixSize, int* matrixColSize, int* returnSize) {
    // 矩阵为空或其中一行为空（如果 matrixSize > 0 但 matrixColSize[0] == 0）
    if (matrixSize == 0 || matrixColSize == NULL || matrixColSize[0] == 0) {
        *returnSize = 0;
        return NULL;
    }

    int rows = matrixSize;
    int cols = matrixColSize[0]; // 假设所有行的列数相同，这是LeetCode的常见设定
    
    // 分配结果数组的内存
    // 总元素数量为 rows * cols
    int* result = (int*)malloc(rows * cols * sizeof(int));
    if (result == NULL) {
        // 内存分配失败处理
        *returnSize = 0;
        return NULL;
    }
    int count = 0; // 记录已添加的元素数量

    // 定义四个边界
    int top = 0;
    int bottom = rows - 1;
    int left = 0;
    int right = cols - 1;

    // 只要还有未遍历的区域，就继续循环
    while (top <= bottom && left <= right) {
        // 1. 从左到右遍历上边界
        for (int col = left; col <= right; ++col) {
            result[count++] = matrix[top][col];
        }
        top++; // 上边界下移

        // 每次边界更新后，立即检查是否还有剩余区域
        if (top > bottom) break;

        // 2. 从上到下遍历右边界
        for (int row = top; row <= bottom; ++row) {
            result[count++] = matrix[row][right];
        }
        right--; // 右边界左移

        // 每次边界更新后，立即检查是否还有剩余区域
        if (left > right) break;

        // 3. 从右到左遍历下边界
        // 注意：只有当 top <= bottom 时才需要遍历，因为 top 已经增加过
        for (int col = right; col >= left; --col) {
            result[count++] = matrix[bottom][col];
        }
        bottom--; // 下边界上移

        // 每次边界更新后，立即检查是否还有剩余区域
        if (top > bottom) break;
        
        // 4. 从下到上遍历左边界
        // 注意：只有当 left <= right 时才需要遍历，因为 right 已经减少过
        for (int row = bottom; row >= top; --row) {
            result[count++] = matrix[row][left];
        }
        left++; // 左边界右移

        // 每次边界更新后，立即检查是否还有剩余区域
        if (left > right) break;
    }

    *returnSize = count; // 设置返回数组的实际大小
    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    std::vector<int> spiralOrder(std::vector<std::vector<int>>& matrix) {
        std::vector<int> result;
        
        // 矩阵为空或其中一行为空，直接返回空结果
        if (matrix.empty() || matrix[0].empty()) {
            return result;
        }

        int rows = matrix.size();
        int cols = matrix[0].size();

        // 定义四个边界
        int top = 0;
        int bottom = rows - 1;
        int left = 0;
        int right = cols - 1;

        // 只要还有未遍历的区域，就继续循环
        while (top <= bottom && left <= right) {
            // 1. 从左到右遍历上边界
            for (int col = left; col <= right; ++col) {
                result.push_back(matrix[top][col]);
            }
            top++; // 上边界下移

            // 检查是否还有剩余区域。例如，单行矩阵遍历完上边界后就结束了。
            if (top > bottom) break;

            // 2. 从上到下遍历右边界
            for (int row = top; row <= bottom; ++row) {
                result.push_back(matrix[row][right]);
            }
            right--; // 右边界左移

            // 检查是否还有剩余区域。例如，单列矩阵遍历完右边界后就结束了。
            if (left > right) break;

            // 3. 从右到左遍历下边界
            // 注意：只有当 top <= bottom 时才需要遍历，因为 top 已经增加过
            for (int col = right; col >= left; --col) {
                result.push_back(matrix[bottom][col]);
            }
            bottom--; // 下边界上移

            // 检查是否还有剩余区域
            if (top > bottom) break;
            
            // 4. 从下到上遍历左边界
            // 注意：只有当 left <= right 时才需要遍历，因为 right 已经减少过
            for (int row = bottom; row >= top; --row) {
                result.push_back(matrix[row][left]);
            }
            left++; // 左边界右移

            // 检查是否还有剩余区域
            if (left > right) break;
        }

        return result;
    }
};
```

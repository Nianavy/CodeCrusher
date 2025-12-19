# Question

[triangle](https://leetcode.cn/problems/triangle/)



# Answer

> solution

解决三角形最小路径和问题的核心思路是动态规划 + 自底向上原地更新。

由于每一步只能走到下一行的相邻位置，从顶部出发的路径选择具有最优子结构：到达某个位置的最小路径和，等于该位置的值加上从它出发到底部的最小路径和。

与其自顶向下递归（需要记忆化、栈空间、额外状态），不如反过来思考：从倒数第二行开始，逐层向上，把每个位置的值更新为“从该位置到底部的最小路径和”。具体做法是：

对于位置 (i, j)，其下一步只能到 (i+1, j) 或 (i+1, j+1)；
因此，triangle[i][j] += min(triangle[i+1][j], triangle[i+1][j+1])；
从最后一行往上推，最终 triangle[0][0] 就是全局最小路径和。
整个过程不需要额外数组，直接在原输入上修改，时间复杂度 O(n²)，额外空间 O(1)。逻辑简单、高效、无递归、无堆分配——用最朴素的循环，解决最本质的问题。

### Way Of C

> source code

```c
/**
 * 给定一个下三角形数组 triangle，返回从顶到底的最小路径和。
 * 每一步只能移动到下一行相邻位置（即 j 或 j+1）。
 *
 * 输入说明：
 * - triangle: 二维数组，triangle[i] 长度为 i+1
 * - triangleSize: 行数 n
 * - triangleColSize: 每行的列数数组（通常 triangleColSize[i] == i+1）
 *
 * 注意：本函数会修改输入数组以节省空间（符合算法题惯例）。
 */
int minimumTotal(int** triangle, int triangleSize, int* triangleColSize) {
    // 从倒数第二行向上遍历
    for (int i = triangleSize - 2; i >= 0; i--) {
        for (int j = 0; j <= i; j++) {
            // 当前位置的最小路径 = 自身 + 下一行两个邻居的较小值
            if (triangle[i + 1][j] < triangle[i + 1][j + 1]) {
                triangle[i][j] += triangle[i + 1][j];
            } else {
                triangle[i][j] += triangle[i + 1][j + 1];
            }
        }
    }
    return triangle[0][0];
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        // 从倒数第二行开始，向上累加最小路径和
        for (int i = triangle.size() - 2; i >= 0; --i) {
            for (int j = 0; j <= i; ++j) {
                triangle[i][j] += min(triangle[i + 1][j], triangle[i + 1][j + 1]);
            }
        }
        return triangle[0][0];
    }
};
```

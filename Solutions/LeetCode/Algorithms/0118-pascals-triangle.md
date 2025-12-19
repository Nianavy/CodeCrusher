# Question

[pascals-triangle](https://leetcode.cn/problems/pascals-triangle/)



# Answer

> solution

生成杨辉三角（Pascal’s Triangle）的核心思路非常直接，完全基于其数学定义：每个数等于它左上方和右上方两个数之和，而每行的首尾元素恒为 1。

具体实现时，按行从上到下构建：
第 0 行只有 [1]；
对于第 i 行（i ≥ 1），创建一个长度为 i+1 的数组；
将第一个和最后一个元素设为 1；
中间的每个位置 j（1 ≤ j ≤ i−1）由上一行的 j−1 和 j 位置相加得到。

C++ 版利用 vector 自动管理内存，代码简洁清晰；
C 版需手动分配二维数组和列宽数组，关键是要正确使用 sizeof(int) 分配列宽数组，并确保每行内存独立分配。

整个算法时间复杂度为 O(n²)，空间复杂度也为 O(n²)（因需返回完整结果），已是最优——没有多余计算，没有冗余结构，每一步都服务于问题本质。这就是好品味：用最朴素的方式，忠实还原数学规则。

### Way Of C

> source code

```c
/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** generate(int numRows, int *returnSize, int** returnColumnSizes) {
    if (numRows <= 0) {
        *returnSize = 0;
        return NULL;
    }

    int **triangle = malloc(numRows * sizeof(int *));
    *returnColumnSizes = malloc(numRows * sizeof(int));  // ← 修正：sizeof(int)，不是 int*
    
    for (int i = 0; i < numRows; i++) {
        int len = i + 1;
        (*returnColumnSizes)[i] = len;
        triangle[i] = malloc(len * sizeof(int));
        triangle[i][0] = 1;
        if (len > 1) {
            triangle[i][len - 1] = 1;
            for (int j = 1; j < len - 1; j++) {
                triangle[i][j] = triangle[i - 1][j - 1] + triangle[i - 1][j];
            }
        }
    }
    *returnSize = numRows;
    return triangle;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> f;
        for (int i = 0; i < numRows; ++i) {
            vector<int> row(i + 1);
            row[0] = row[i] = 1;
            for (int j = 1; j < i; ++j)
                row[j] = f[i - 1][j - 1] + f[i - 1][j];
            f.push_back(row);
        }
        return f;
    }
};
```

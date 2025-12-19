# Question

[pascals-triangle-ii](https://leetcode.cn/problems/pascals-triangle-ii/)



# Answer

> solution

要高效生成杨辉三角的第 rowIndex 行（0-indexed），关键在于只用一个一维数组，原地更新，避免存储整张三角。

核心思路基于组合数的递推关系：
第 i 行第 j 个数 = 第 i-1 行第 j-1 个数 + 第 i-1 行第 j 个数。

由于我们只关心最终一行，可以复用同一个数组。但若从左往右更新，会覆盖后续计算所需的旧值。因此，必须从右向左逆序更新：从 j = i 递减到 1，执行 row[j] += row[j - 1]。

初始化只需 row[0] = 1，其余为 0。随着每轮迭代，正确的数值会逐步“传播”到右侧，自然形成该行的首尾为 1、中间为和的结构，无需显式设置边界。

整个过程时间复杂度 O(n²)，空间复杂度 O(n)（仅输出所需），无多余分配、无冗余判断——简洁、高效、符合数学本质。这就是好代码：用最直接的方式，做最必要的事。

### Way Of C

> source code

```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* getRow(int rowIndex, int* returnSize) {
    *returnSize = rowIndex + 1;
    int* row = calloc(rowIndex + 1, sizeof(int)); // 初始化为 0
    row[0] = 1;

    for (int i = 1; i <= rowIndex; i++) {
        for (int j = i; j >= 1; j--) {
            row[j] += row[j - 1];
        }
    }
    return row;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> row(rowIndex + 1, 0);
        row[0] = 1;
        for (int i = 1; i <= rowIndex; ++i) {
            for (int j = i; j >= 1; --j) {
                row[j] += row[j - 1];
            }
        }
        return row;
    }
};
```

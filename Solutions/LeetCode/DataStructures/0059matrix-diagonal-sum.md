https://leetcode.cn/problems/matrix-diagonal-sum

### Think
```TXT
给定一个 n x n 的矩阵，计算其主对角线和副对角线元素的和。如果 n 为奇数，中心元素会被两次累加，需要减去一次。

方法思路
确定矩阵大小：获取矩阵的大小 n。

初始化总和：创建一个变量 sum 来存储对角线元素的和。

遍历矩阵：通过循环遍历矩阵的每一行和列，累加主对角线元素 mat[i][i] 和副对角线元素 mat[i][n-1-i]。

处理奇数情况：如果 n 为奇数，减去中心元素 mat[mid][mid] 一次，其中 mid = n / 2。

返回结果：返回计算得到的 sum，即对角线元素的和。

变量定义:

n: 矩阵的大小。

sum: 存储对角线元素的和。

mid: 中心元素的位置，仅在 n 为奇数时使用。

循环累加:

使用 for 循环遍历矩阵的每一行 i。

累加主对角线元素 mat[i][i] 和副对角线元素 mat[i][n-1-i]。

处理奇数情况:

利用位运算 (n & 1) 判断 n 是否为奇数。

如果 n 为奇数，减去中心元素 mat[mid][mid] 一次。

返回结果:

返回计算得到的 sum，即对角线元素的和。
```

### Ways
By C++
```CC
class Solution {
public:
    int diagonalSum(vector<vector<int>>& mat) {
        int n = mat.size(), sum = 0, mid = n / 2;
        for (int i=0; i<n; ++i)
            sum += mat[i][i] + mat[i][n-1-i];
        return sum - mat[mid][mid] * (n & 1);
    }
};
```

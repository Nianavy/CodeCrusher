https://leetcode.cn/problems/reshape-the-matrix

### Think
```TXT
检查元素个数：
  获取原矩阵的行数 m 和列数 n。
  检查 m * n 是否等于 r * c，如果不等，直接返回原矩阵 mat。

创建新矩阵：
  创建一个大小为 r x c 的新矩阵 ans，并初始化其所有元素为0。

重塑矩阵：
  使用一个循环遍历原矩阵的所有元素（从0到 m * n - 1）。
  对于每个元素 x，计算其在新矩阵 ans 中的位置为 x / c 行和 x % c 列。
  同时，计算该元素在原矩阵 mat 中的位置为 x / n 行和 x % n 列。
  将原矩阵中对应位置的元素赋值到新矩阵的相应位置。

返回新矩阵：
  循环结束后，返回新矩阵 ans。
```

### Ways
By C++
```C++
class Solution {
public:
    vector<vector<int>> matrixReshape(vector<vector<int>>& mat, int r, int c) {
        int m = mat.size();
        int n = mat[0].size();
        if (m * n != r * c) return mat;
        vector<vector<int>> ans(r, vector<int>(c));
        for (int x = 0; x < m * n; ++x)
            ans[x / c][x % c] = mat[x / n][x % n];
        return ans;
    }
};
```

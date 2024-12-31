https://leetcode.cn/problems/transpose-matrix

### Think
```TXT
获取矩阵的行数和列数：
使用 matrix.size() 获取矩阵的行数 m。
使用 matrix[0].size() 获取矩阵的列数 n。

初始化转置矩阵：
创建一个新的二维数组 transposed，大小为 n x m（即原矩阵的列数 x 行数）。

遍历原矩阵并赋值到转置矩阵：
使用双重循环遍历原矩阵的每个元素。
将原矩阵的 matrix[i][j] 赋值给转置矩阵的 transposed[j][i]，实现行列互换。

返回转置矩阵：
返回计算完成的转置矩阵。
```


### Ways
By C++
```CPP
class Solution {
public:
    vector<vector<int>> transpose(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> transposed(n, vector<int>(m));
        for (int i=0; i<m; ++i) {
            for (int j = 0; j < n; ++j) {
                transposed[j][i] = matrix[i][j];
            }
        }
        return transposed;
    }
};
```

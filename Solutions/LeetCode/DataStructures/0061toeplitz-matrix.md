https://leetcode.cn/problems/toeplitz-matrix

### Think
```TXT
遍历矩阵的每一个元素（从第二行和第二列开始，即 i = 1 和 j = 1）。
检查当前元素 matrix[i][j] 是否等于其左上角的元素 matrix[i-1][j-1]。
  如果发现不相等，则矩阵不是托普利茨矩阵，直接返回 false。
  如果所有元素都满足条件，则返回 true。
```

### Ways
By C++
```C++
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                if (matrix[i][j] != matrix[i-1][j-1])
                    return false;
            }
        }
        return true;
    }
};
```

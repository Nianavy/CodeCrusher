https://www.acwing.com/problem/content/798/

### Think
```
prefixsum
```

### Ways
By C++
```C++
#include <cstdio>
#include <vector>

using namespace std;

vector<vector<int>> calculate2DPrefixSum(const vector<vector<int>>& matrix) {
  int n = matrix.size();      
  int m = matrix[0].size();   
  vector<vector<int>> prefixSum(n + 1, vector<int>(m + 1, 0));

  for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) {
      prefixSum[i][j] = matrix[i - 1][j - 1] + prefixSum[i - 1][j] + prefixSum[i][j - 1] - prefixSum[i - 1][j - 1];
    }
  }

  return prefixSum;
}

int main() {
  int n, m, q;
  scanf("%d%d%d", &n, &m, &q);

  vector<vector<int>> matrix(n, vector<int>(m));
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < m; ++j) {
      scanf("%d", &matrix[i][j]);
    }
  }

  vector<vector<int>> prefixSum = calculate2DPrefixSum(matrix);

  while (q--) {
    int x1, y1, x2, y2;
    scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
    printf("%d\n", prefixSum[x2][y2] - prefixSum[x1 - 1][y2] - prefixSum[x2][y1 - 1] + prefixSum[x1 - 1][y1 - 1]);
  }

  return 0;
}
```

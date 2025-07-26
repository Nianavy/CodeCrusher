https://www.acwing.com/problem/content/3717/

### Think
```
DP + Pruning:
Enumerate all possible step sizes,
partition the array into subsequences with the given step interval,
use dynamic programming to compute the longest non-decreasing contiguous segment in each subsequence from back to front,
and maintain the global maximum.
Optimize computation with pruning and early stopping.
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 1010;
int n, w[N], dp[N];

int main() {
  while (scanf("%d", &n) != -1) {
    for (int i = 0; i < n; ++i) scanf("%d", &w[i]);

    int res = 1;

    for (int d = 1; d < n; ++d) {
      int maxLenPossible = (n + d - 1) / d;
      if (maxLenPossible <= res)
        continue;

      for (int a = 0; a < d; ++a) {
        for (int i = ((n - 1 - a) / d) * d + a; i >= a; i -= d) {
          if (i + d < n && w[i] <= w[i + d])
            dp[i] = dp[i + d] + 1;
          else
            dp[i] = 1;

          if (dp[i] > res) {
            res = dp[i];
            if (res == n) break;
          }
        }
        if (res == n) break;
      }
      if (res == n) break;
    }

    printf("%d\n", n - res);
  }
  return 0;
}
```

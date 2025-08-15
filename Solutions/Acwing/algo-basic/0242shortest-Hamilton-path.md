https://www.acwing.com/problem/content/93/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 20, M = 1 << N;
int n, w[N][N], f[M][N];

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i)
    for (int j = 0; j < n; ++j)
      scanf("%d", &w[i][j]);
      
  memset(f, 0x3f, sizeof f);
  f[1][0] = 0;
  for (int i = 0; i < 1 << n; ++i)
    for (int j = 0; j < n; ++j)
      if (i >> j & 1)
        for (int k = 0; k < n; ++k)
          if (i >> k & 1) f[i][j] = std::min(f[i][j], f[i - (1 << j)][k] + w[k][j]);
  printf("%d", f[(1 << n) - 1][n - 1]);
  return 0;
}
```

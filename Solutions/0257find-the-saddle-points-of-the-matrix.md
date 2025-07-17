https://www.acwing.com/problem/content/3711/

### Think
```
Row maximum column minimum element search.
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 15;
int n, m;
int g[N][N];

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= m; ++j)
      scanf("%d", &g[i][j]);
  
  int cnt = 0;
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= m; ++j) {
      bool fr = true, fc = true;
      for (int k = 1; k <= m; ++k)
        if (g[i][k] > g[i][j])
          fr = false;
      for (int k = 1; k <= n; ++k)
        if (g[k][j] < g[i][j])
          fc = false;
      if (fr && fc) {
        ++cnt;
        printf("%d %d %d\n", i, j, g[i][j]);
      }
    }
  if (!cnt) puts("NO");
  return 0;
}
```

https://www.acwing.com/problem/content/903/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 310;
int n, m, g[N][N], f[N][N];
int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};

int dp(int x, int y) {
  int &v = f[x][y];
  if (v != -1) return v;
  v = 1;
  for (int i = 0; i < 4; ++i) {
    int a = x + dx[i], b = y + dy[i];
    if (a >= 1 && a <= n && b >= 1 && b <= m && g[x][y] > g[a][b]) v = std::max(v, dp(a, b) + 1);
  }
  return v;
}

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= m; ++j)
       scanf("%d", &g[i][j]);
  memset(f, -1, sizeof f);
  int res = 0;
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= m; ++j)
      res = std::max(res, dp(i, j));
  printf("%d\n", res);
  return 0;
}
```

https://www.acwing.com/problem/content/9/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 110;
int v[N][N], w[N][N], s[N], f[N], n, m;

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i) {
    scanf("%d", &s[i]);
    for (int j = 0; j < s[i]; ++j)
      scanf("%d%d", &v[i][j], &w[i][j]);
  }
  for (int i = 1; i <= n; ++i)
    for (int j = m; j >= 0; --j)
      for (int k = 0; k < s[i]; ++k)
        if (v[i][k] <= j) f[j] = std::max(f[j], f[j - v[i][k]] + w[i][k]);
  printf("%d\n", f[m]);
  return 0;
}
```

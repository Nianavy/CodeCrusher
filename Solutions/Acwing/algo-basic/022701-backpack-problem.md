https://www.acwing.com/problem/content/2/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 1010;
int v[N], w[N], f[N], n, m;

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i) scanf("%d%d", &v[i], &w[i]);
  for (int i = 1; i <= n; ++i)
    for (int j = m; j >= v[i]; --j)
      f[j] = std::max(f[j], f[j - v[i]] + w[i]);
  printf("%d\n", f[m]);
  return 0;
}
```

https://www.acwing.com/problem/content/5/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 12010, M = 2010;
int v[N], w[N], f[M], n, m;

int main() {
  scanf("%d%d", &n, &m);
  int cnt = 0;
  for (int i = 1; i <= n; ++i) {
    int a, b, s;
    scanf("%d%d%d", &a, &b, &s);
    int k = 1;
    while (k <= s) {
      ++cnt;
      v[cnt] = a * k;
      w[cnt] = b * k;
      s -= k;
      k *= 2;
    }
    if (s > 0) {
      ++cnt;
      v[cnt] = a * s;
      w[cnt] = b * s;
    }
  }
  n = cnt;
  for (int i = 1; i <= n; ++i)
    for (int j = m; j >= v[i]; --j)
      f[j] = std::max(f[j], f[j - v[i]] + w[i]);
  printf("%d\n", f[m]);
  return 0;
}
```

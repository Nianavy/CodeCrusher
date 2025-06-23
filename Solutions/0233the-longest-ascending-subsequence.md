https://www.acwing.com/problem/content/897/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 1010;
int a[N], f[N], n;

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; ++i) scanf("%d", &a[i]);
  for (int i = 1; i <= n; ++i) {
    f[i] = 1;
    for (int j = 1; j < i; ++j)
      if (a[j] < a[i]) f[i] = std::max(f[i], f[j] + 1);
  }
  int res = 0;
  for (int i = 1; i <= n; ++i) res = std::max(res, f[i]);
  printf("%d\n", res);
  return 0;
}
```

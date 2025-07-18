https://www.acwing.com/problem/content/3712/

### Think
```
Arrays simulate everything
Pairwise Swap
Interval exchange
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 110;
int n, w[N];

int main() {
  scanf("%d", &n);
  for (int i = 0; i <= n; ++i) scanf("%d", &w[i]);
  for (int i = 0; i < n - 1; i += 2) std::swap(w[i], w[i+1]);
  for (int i = 0; i < n; ++i) printf("%d ", w[i]);
  return 0;
}
```

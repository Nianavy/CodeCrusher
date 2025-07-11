https://www.acwing.com/problem/content/106/

### Think
```
greedy algorithm
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 1e5 + 10;
int q[N], n;

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &q[i]);
  std::sort(q, q + n);
  int res = 0;
  for (int i = 0; i < n; ++i) res += std::abs(q[i] - q[n / 2]);
  printf("%d\n", res);
  return 0;
}
```

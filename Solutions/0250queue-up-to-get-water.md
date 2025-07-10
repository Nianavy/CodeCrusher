https://www.acwing.com/problem/content/description/915/

### Think
```
greedy algorithm
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

typedef long long LL;
const int N =1e5 + 10;
int t[N], n;

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &t[i]);
  std::sort(t, t + n);
  std::reverse(t, t + n);
  LL res = 0;
  for (int i = 0; i < n; ++i) res += t[i] * i;
  printf("%lld\n", res);
  return 0;
}
```

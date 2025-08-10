https://www.acwing.com/problem/content/3701/

### Think
```
For each position,
extend from smaller previous elements to compute LIS length.
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 510;
int n, w[N], f[N];

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &w[i]);
  
  int res = 0;
  for (int i = 0; i < n; ++i) {
    f[i] = 1;
    for (int j = 0; j < i; ++j)
      if (w[j] < w[i]) f[i] = std::max(f[i], f[j] + 1);
    res = std::max(res, f[i]);
  }
  
  printf("%d\n", res);
  
  return 0;
}
```

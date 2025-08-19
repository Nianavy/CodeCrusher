https://www.acwing.com/problem/content/3589/

```txt
Simulate shuffling via four reversals:
reverse two segments,
reverse whole,
then reverse first half.
```

```c
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 1010;
int n, m, w[N];

int main() {
  scanf("%d %d", &n, &m);
  for (int i = 0; i <= n; ++i) w[i] = i;
  while (m--) {
    int x;
    scanf("%d", &x);
    std::reverse(w + 1, w + x + 1);
    std::reverse(w + x + 1, w + n + 1);
    std::reverse(w + 1, w + n + 1);
    std::reverse(w + 1, w + n / 2 + 1);
  }
  for (int i = 1; i <= n; ++i) printf("%d ", w[i]);
  return 0;
}
```

https://www.acwing.com/problem/content/722/

```c++
#include <cstdio>

int main() {
  int sum = 0, n, a;
  scanf("%d", &a);
  while (scanf("%d", &n) == 1, n <= 0);
  for (int i = 0; i < n; ++i) sum += a + i;
  printf("%d\n", sum);
  return 0;
}
```

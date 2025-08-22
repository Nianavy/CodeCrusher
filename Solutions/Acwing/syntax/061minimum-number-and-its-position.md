https://www.acwing.com/problem/content/744/

```c++
#include <cstdio>

int main() {
  int n, p = 0, x[1000];
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &x[i]);
  for (int i = 0; i < n; ++i)
    if (x[i] < x[p]) p = i;
  printf("Minimum value: %d\nPosition: %d\n", x[p], p);
  return 0;
}
```

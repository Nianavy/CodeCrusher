https://www.acwing.com/problem/content/741/

```c++
#include <cstdio>

int main() {
  double a[100];
  for (int i = 0; i < 100; ++i) scanf("%d", &a[i]);
  for (int i = 0; i < 100; ++i)
    if (a[i] <= 10) printf("A[%d] = %d\n", i, a[i]);
  return 0;
}
```

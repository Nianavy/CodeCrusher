https://www.acwing.com/problem/content/740/

```c++
#include <cstdio>

int main() {
  int v[10];
  scanf("%d", &v[0]);
  for (int i = 1; i < 10; ++i) v[i] = v[i - 1] * 2;
  for (int i = 0; i < 10; ++i) printf("N[%d] = %d\n", i, v[i]);
  return 0;
}
```

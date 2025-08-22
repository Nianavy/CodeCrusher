https://www.acwing.com/problem/content/711/

```c++
#include <cstdio>

int x;

int main() {
  scanf("%d", &x);
  for (int i = 1; i <= x; ++i)
    if (i % 2 != 0) printf("%d\n", i);
  return 0;
}
```

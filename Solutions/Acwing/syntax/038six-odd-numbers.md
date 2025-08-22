https://www.acwing.com/problem/content/712/

```c++
#include <cstdio>

int x;

int main() {
  scanf("%d", &x);
  for (int i = x, j = 0; j < 6; ++i) {
    if (i % 2) {
      printf("%d\n", i);
      ++j;
    }
  }
  return 0;
}
```

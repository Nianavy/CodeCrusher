https://www.acwing.com/problem/content/723/

```c++
#include <cstdio>

int main() {
  int x;
  while (scanf("%d", &x), x) {
    for (int i = 1; i <= x; ++i) printf("%d ", i);
    puts("");
  }
  return 0;
}
```

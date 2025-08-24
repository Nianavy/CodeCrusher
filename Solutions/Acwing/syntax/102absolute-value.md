https://www.acwing.com/problem/content/812/

```c++
#include <cstdio>

int abs(int x) {
  return x > 0 ? x : -x;
}

int main() {
  int x;
  scanf("%d", &x);
  printf("%d\n", abs(x));
  return 0;
}
```

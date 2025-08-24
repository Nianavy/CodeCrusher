https://www.acwing.com/problem/content/821/

```c++
#include <cstdio>

int fac(int n) {
  return n > 1 ? fac(n - 1) * n : 1;
}

int main() {
  int n;
  scanf("%d", &n);
  printf("%d\n", fac(n));
  return 0;
}
```

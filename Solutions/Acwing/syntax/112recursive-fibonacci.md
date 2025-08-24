https://www.acwing.com/problem/content/822/

```c++
#include <cstdio>

int fib(int n) {
  return n <= 2 ? 1 : fib(n - 2) + fib(n - 1);
}

int main() {
  int n;
  scanf("%d", &n);
  printf("%d\n", fib(n));
  return 0;
}
```

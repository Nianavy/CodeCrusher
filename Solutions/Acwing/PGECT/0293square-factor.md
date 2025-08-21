https://www.acwing.com/problem/content/3592/

```txt
Checks if a number has a square factor by testing divisibility with squares of integers.
```

```c++
#include <cstdio>

int main() {
  int n;
  while (scanf("%d", &n) == 1) {
    bool res = false;
    for (int i = 2; i * i <= n; ++i)
      if (n % (i * i) == 0) {
        res = true;
        break;
      }
    if (res) puts("Yes");
    else puts("No");
  }
  return 0;
}
```

https://www.acwing.com/problem/content/727/

```c++
#include <cstdio>

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int x, sum = 0;
    scanf("%d", &x);
    for (int i = 1; i <= x / i; ++i) {
      if (x % i == 0) {
        if (i < x) sum += i;
        if (x / i < x) sum += x / i;
      }
    }
    if (sum == x) printf("%d is perfect\n", x);
    else printf("%d is not perfect\n", x);
  }
  return 0;
}
```

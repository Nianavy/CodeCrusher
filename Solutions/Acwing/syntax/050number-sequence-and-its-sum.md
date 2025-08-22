https://www.acwing.com/problem/content/724/

```c++
#include <cstdio>

void swap_(int &a, int &b) {
  int t = a;
  a = b;
  b = t;
}

int main() {
  int n, m;
  while (scanf("%d %d", &n, &m) == 2, n > 0 && m > 0) {
    if (n > m) swap_(n, m);
    int s = 0;
    for (int i = n; i <= m; ++i) {
      printf("%d ", i);
      s += i;
    }
    printf("Sum=%d\n", s);
  }
  return 0;
}
```

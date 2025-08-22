https://www.acwing.com/problem/content/757/

```c++
#include <cstdio>

int main() {
  int n, q[101][101];
  while (scanf("%d", &n) == 1, n) {
    for (int i = 0; i < n; ++i) {
      for (int j = 0; j < n; ++j) {
        q[i][j] = 1;
        for (int k = 0; k < i + j; ++k) q[i][j] *= 2;
      }
    }
    for (int i = 0; i < n; ++i) {
      for (int j = 0; j < n; ++j) printf("%d ", q[i][j]);
      puts("");
    }
    puts("");
  }
  return 0;
}
```

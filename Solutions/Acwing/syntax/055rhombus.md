https://www.acwing.com/problem/content/729/

```c++
#include <cstdio>
#include <cmath>

int main() {
  int n;
  scanf("%d", &n);
  int cx = n / 2, cy = n / 2;
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (abs(cx - i) + abs(cy - j) <= n / 2) printf("*");
      else printf(" ");
    }
    puts("");
  }
  return 0;
}
```

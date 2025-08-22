https://www.acwing.com/problem/content/721/

```c++
#include <cstdio>

void swap_(int &a, int &b) {
  int p = a;
  a = b;
  b = p;
}

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int x, y;
    scanf("%d %d", &x, &y);
    if (x > y) swap_(x, y);
    int sum = 0;
    for (int i = x + 1; i < y; ++i) {
      if (i % 2) sum += i;
    }
    printf("%d\n", sum);
  }
  return 0;
}
```

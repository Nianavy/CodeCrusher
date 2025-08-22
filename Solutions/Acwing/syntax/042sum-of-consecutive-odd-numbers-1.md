https://www.acwing.com/problem/content/716/

```c++
#include <cstdio>

void swap_(int &a, int &b) {
  int temp = a;
  a = b;
  b = temp;
}

int main() {
  int x, y, z = 0;
  scanf("%d %d", &x, &y);
  if (x > y) swap_(x, y);
  for (int i = x + 1; i < y; ++i)
    if (i % 2) z += i;
  printf("%d\n", z);
  return 0;
}
```

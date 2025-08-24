https://www.acwing.com/problem/content/809/

```c++
#include <cstdio>

int sum(int l, int r) {
  int sum = 0;
  for (int i = l; i <= r; ++i) sum += i;
  return sum;
}

int main() {
  int l, r;
  scanf("%d %d", &l, &r);
  printf("%d\n", sum(l ,r));
  return 0;
}
```

https://www.acwing.com/problem/content/813/

```c++
#include <cstdio>

void swap(int &x, int &b) {
  int t = x;
  x = b;
  b = t;
}

int main() {
  int a, b;
  scanf("%d %d", &a, &b);
  swap(a, b);
  printf("%d %d\n", a, b);
  return 0;
}
```

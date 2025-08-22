https://www.acwing.com/problem/content/719/

```c++
#include <cstdio>

int main() {
  int n, a = 0, b = 1;
  scanf("%d", &n);
  while (n--) {
    printf("%d ", a);
    int c = a + b;
    a = b;
    b = c;
  }
  return 0;
}
```

https://www.acwing.com/problem/content/717/

```c++
#include <cstdio>

int main() {
  int n;
  scanf("%d", &n);
  for (int i = 2; i < 10000; ++i)
    if (i % n == 2) printf("%d\n", i);
  return 0;
}
```

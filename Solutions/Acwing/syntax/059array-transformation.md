https://www.acwing.com/problem/content/742/

```c++
#include <cstdio>

int main() {
  int n[20];
  for (int i = 0; i < 20; ++i) scanf("%d", &n[i]);
  int l = 0, r = 19;
  while (l <= r) {
    int tmp = n[l];
    n[l] = n[r];
    n[r] = tmp;
    ++l;
    --r;
  }
  for (int i = 0; i < 20; ++i) printf("N[%d] = %d\n", i, n[i]);
  return 0;
}
```

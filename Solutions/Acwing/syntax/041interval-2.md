https://www.acwing.com/problem/content/715/

```c++
#include <cstdio>

int main() {
  int n, i = 0, o = 0;
  scanf("%d", &n);
  for (int m = 0; m < n; ++m) {
    int x;
    scanf("%d", &x);
    if (x >= 10 && x <= 20) ++i;
    else ++o;
  }
  printf("%d in \n%d out\n", i, o);
  
  return 0;
}
```

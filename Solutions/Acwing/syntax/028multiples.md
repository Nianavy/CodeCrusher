https://www.acwing.com/problem/content/667/

```c++
#include <cstdio>

int main() {
  int a, b;
  scanf("%d %d", &a, &b);
  if (a % b == 0 || b % a == 0) puts("Sao Multiplos");
  else puts("Nao sao Multiplos");
  return 0;
}
```

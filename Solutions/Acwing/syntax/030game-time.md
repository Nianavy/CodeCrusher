https://www.acwing.com/problem/content/669/

```c++
#include <cstdio>

int main() {
  int a, b;
  scanf("%d %d", &a, &b);
  if (a > b) printf("O JOGO DUROU %d HORA(S)\n", b - a + 24);
  else if (a < b) printf("O JOGO DUROU %d HORA(S)", b - a);
  else puts("O JOGO DUROU 24 HORA(S)");
  return 0;
}
```

https://www.acwing.com/problem/content/670/

```c++
#include <cstdio>

int main() {
  int a, b, c, d;
  scanf("%d %d %d %d", &a, &b, &c, &d);
  int sum_m = (c - a) * 60 + d - b;
  if (sum_m <= 0) sum_m += 60 * 24;
  int h = sum_m / 60;
  int m = sum_m % 60;
  printf("O JOGO DUROU %d HORA(S) E %d MINUTO(S)\n", h, m);
  return 0;
}
```

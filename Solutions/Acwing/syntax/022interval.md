https://www.acwing.com/problem/content/661/

```c++
#include <cstdio>

int main() {
  double x;
  scanf("%lf", &x);
  if (x < 0 || x > 100) puts("Fora de intervalo");
  else {
    if (x >= 0 && x <= 25) puts("Intervalo [0,25]");
    if (x > 25 && x <= 50) puts("Intervalo (25,50]");
    if (x > 50 && x <= 75) puts("Intervalo (50,75]");
    if (x > 75 && x <= 100) puts("Intervalo (75,100]");
  }
  return 0;
}
```

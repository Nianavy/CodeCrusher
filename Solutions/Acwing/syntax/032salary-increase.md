https://www.acwing.com/problem/content/671/

```c++
#include <cstdio>

int main() {
  double s;
  scanf("%lf", &s);
  if (s >= 0 && s <= 400)
      printf("Novo salario: %.2lf\nReajuste ganho: %.2lf\nEm percentual: %d %%\n", s + s * 0.15, s * 0.15, 15);
  else if (s >= 400.01 && s <= 800)
      printf("Novo salario: %.2lf\nReajuste ganho: %.2lf\nEm percentual: %d %%\n", s + s * 0.12, s * 0.12, 12);
  else if (s >= 800.01 && s <= 1200)
      printf("Novo salario: %.2lf\nReajuste ganho: %.2lf\nEm percentual: %d %%\n", s + s * 0.1, s * 0.1, 10);
  else if (s >= 1200.01 && s <= 2000)
      printf("Novo salario: %.2lf\nReajuste ganho: %.2lf\nEm percentual: %d %%\n", s + s * 0.07, s * 0.07, 7);
  else
      printf("Novo salario: %.2lf\nReajuste ganho: %.2lf\nEm percentual: %d %%\n", s + s * 0.04, s * 0.04, 4);
  return 0;
}
```

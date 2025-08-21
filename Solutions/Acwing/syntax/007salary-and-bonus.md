https://www.acwing.com/problem/content/612/

```c++
#include <cstdio>

char name[11];
double base, present;

int main() {
  scanf("%s %lf %lf", name, &base, &present);
  printf("TOTAL = R$ %.2lf\n", base + 0.15 * present);
  return 0;
}
```

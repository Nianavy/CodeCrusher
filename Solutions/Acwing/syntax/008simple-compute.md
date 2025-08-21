https://www.acwing.com/problem/content/613/

```c++
#include <cstdio>
int a, b, x, y;
double c, z;

int main() {
  scanf("%d %d %lf", &a, &b, &c);
  scanf("%d %d %lf", &x, &y, &z);
  printf("VALOR A PAGAR: R$ %.2lf\n", b * c + y * z);
  return 0;
}
```

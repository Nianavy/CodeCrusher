https://www.acwing.com/problem/content/660/

```c++
#include <cstdio>
#include <cmath>

int main() {
  double a, b, c;
  scanf("%lf %lf %lf", &a, &b, &c);
  double key = b * b - 4 * a * c;
  if (key < 0 || a == 0) puts("Impossivel calcular");
  else {
    double r1 = (-1 * b + sqrt(key)) / (2 * a), r2 = (-1 * b - sqrt(key)) / (2 * a);
    printf("R1 = %.5lf\nR2 = %.5lf\n", r1, r2);
  }
  return 0;
}
```

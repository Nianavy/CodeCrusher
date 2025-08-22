https://www.acwing.com/problem/content/668/

```c++
#include <cstdio>

void swap_(double &a, double &b) {
  double temp = a;
  a = b;
  b = temp;
}

int main() {
  double a, b, c;
  scanf("%lf %lf %lf", &a, &b, &c);
  if (a < b) swap_(a, b);
  if (a < c) swap_(a, c);
  if (b < c) swap_(b, c);
  if (a >= b + c) puts("NAO FORMA TRIANGULO");
  else {
    if (a * a == b * b + c * c) puts("TRIANGULO RETANGULO");
    if (a * a > b * b + c * c) puts("TRIANGULO OBTUSANGULO");
    if (a * a < b * b + c * c) puts("TRIANGULO ACUTANGULO");
    if (a == b && b == c) puts("TRIANGULO EQUILATERO");
    else if  (a == b || b == c) puts("TRIANGULO ISOSCELES");
  }
  return 0;
}
```

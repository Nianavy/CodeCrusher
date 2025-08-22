https://www.acwing.com/problem/content/658/

```c++
#include <cstdio>

int main() {
  double n;
  int x[6] = {100, 50, 20, 10, 5, 2}, y[6] = {100, 50, 25, 10, 5, 1};
  scanf("%lf", &n);
  int m = n * 100;
  
  puts("NOTAS:");
  for (int i = 0; i < 6; ++i) {
    printf("%d nota(s) de R$ %.2lf\n", m / (x[i] * 100), (double)x[i]);
    m %= x[i] * 100;
  }
  
  puts("MOEDAS:");
  for (int j = 0; j < 6; ++j) {
    printf("%d moeda(s) de R$ %.2lf\n", m / y[j], y[j] / 100.0);
    m %= y[j];
  }
  
  return 0;
}
```

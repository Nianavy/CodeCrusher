https://www.acwing.com/problem/content/663/

```c++
#include <cstdio>

int main() {
  double a, b, c, d;
  scanf("%lf %lf %lf %lf", &a, &b, &c, &d);
  double media = (a * 2 + b * 3 + c * 4 + d * 1) / 10;
  printf("Media: %.1lf\n", media);
  
  if (media >= 7.0) puts("Aluno aprovado.");
  else if (media < 5.0) puts("Aluno reprovado.");
  else if (media >= 5.0 && media < 7.0) {
    puts("Aluno em exame.");
    double x;
    scanf("%lf", &x);
    printf("Nota do exame: %.1lf\n", x);
    double aver = (media + x) / 2;
    if (aver >= 5.0) puts("Aluno aprovado.");
    else puts("Aluno reprovado.");
    printf("Media final: %.1lf\n", aver);
  }
  return 0;
}
```

https://www.acwing.com/problem/content/745/

```c++
#include <cstdio>

int main() {
  double s = 0, m[12][12];
  int l;
  char x;
  scanf("%d %c", &l, &x);
  for (int i = 0; i < 12; ++i)
    for (int j = 0; j < 12; ++j)
        scanf("%lf", &m[i][j]);
  for (int i = 0; i < 12; ++i) s += m[l][i];
  if (x == 'M') s /= 12;
  printf("%.1lf\n", s);
}
```

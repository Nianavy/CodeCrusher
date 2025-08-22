https://www.acwing.com/problem/content/747/

```c++
#include <cstdio>

int main() {
  double s = 0, m[12][12];
  char x;
  scanf("%c", &x);
  for (int i = 0; i < 12; ++i)
    for (int j = 0; j < 12; ++j)
        scanf("%lf", &m[i][j]);
  for (int i = 0; i < 12; ++i)
    for (int j = i + 1; j < 12; ++j)
        s += m[i][j];
  if (x == 'M') s /= 66;
  printf("%.1lf\n", s);
  return 0;
}
```

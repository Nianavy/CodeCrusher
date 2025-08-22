https://www.acwing.com/problem/content/714/

```c++
#include <cstdio>

int sum = 0;

int main() {
  for (int i = 0; i < 6; ++i) {
    double x;
    scanf("%lf", &x);
    if (x > 0) ++sum;
  }
  printf("%d positive numbers\n", sum);
}
```

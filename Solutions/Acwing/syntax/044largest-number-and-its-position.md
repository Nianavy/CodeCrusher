https://www.acwing.com/problem/content/718/

```c++
#include <cstdio>

int main() {
  int in, index, max = 0;
  for (int i = 1; i <= 100; ++i) {
    scanf("%d", &in);
    if (in > max) {
      max = in;
      index = i;
    }
  }
  printf("%d\n%d\n", max, index);
  return 0;
}
```

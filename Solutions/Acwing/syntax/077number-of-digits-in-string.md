https://www.acwing.com/problem/content/763/

```c++
#include <cstdio>

int main() {
  char c;
  int cnt = 0;
  while (scanf("%c", &c) == 1) {
    if (c >= '0' && c <= '9') ++cnt;
  }
  printf("%d\n", cnt);
  return 0;
}
```

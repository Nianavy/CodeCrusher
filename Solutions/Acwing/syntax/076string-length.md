https://www.acwing.com/problem/content/762/

```c++
#include <cstdio>

char statement[101];

int main() {
  scanf("%100[^\n]%*c", statement);
  int cnt = 0;
  for (int i = 0; statement[i] != '\0'; ++i) ++cnt;
  printf("%d\n", cnt);
  return 0;
}
```

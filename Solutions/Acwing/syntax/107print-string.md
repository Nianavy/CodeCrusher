https://www.acwing.com/problem/content/817/

```c++
#include <cstdio>

void print(const char *str) {
  printf("%s", str);
}

int main() {
  char str[101]; 
  scanf("%100[^\n]%*c", str);
  print(str);
  return 0;
}
```

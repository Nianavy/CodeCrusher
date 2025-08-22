https://www.acwing.com/problem/content/769/

```c++
#include <cstdio>

int main() {
  char str[101];
  scanf("%100[^\n]%*c", str);
  for (char &c: str)
    if (c >= 'a' && c <= 'z') c = (c + 1 - 'a') % 26 + 'a';
    else if (c >= 'A' && c <= 'Z') c = (c + 1 - 'A') % 26 + 'A';
  printf("%s\n", str);
  return 0;
}
```

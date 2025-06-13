https://www.acwing.com/problem/content/893/

### Think
```
Impartial Combinatorial Games
```

### Ways
```C++
#include <cstdio>

const int N = 1e5 + 10;

int main() {
  int n;
  scanf("%d", &n);
  int res = 0;
  while (n--) {
    int x;
    scanf("%d", &x);
    res ^= x;
  }
  if (res) puts("Yes");
  else puts("No");
  return 0;
}
```

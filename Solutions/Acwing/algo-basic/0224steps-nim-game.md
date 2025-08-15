https://www.acwing.com/problem/content/894/

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
  for (int i = 1; i <= n; ++i) {
    int x;
    scanf("%d", &x);
    if (i & 1) res ^= x;
  }
  if (res) puts("Yes");
  else puts("No");
  return 0;
}
```

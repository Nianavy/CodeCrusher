https://www.acwing.com/problem/content/899/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 1010;
int f[N][N], n, m;
char a[N], b[N];

int main() {
  scanf("%d%d", &n, &m);
  scanf("%s%s", a + 1, b + 1);
  for (int i = 1; i <= n; ++i) 
    for (int j = 1; j <=m; ++j) {
      f[i][j] = std::max(f[i - 1][j], f[i][j - 1]);
      if (a[i] == b[j]) f[i][j] = std::max(f[i][j], f[i - 1][j - 1] + 1); 
    }
  printf("%d\n", f[n][m]);
  return 0;
}
```

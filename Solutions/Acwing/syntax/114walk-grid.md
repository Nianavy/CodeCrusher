https://www.acwing.com/problem/content/824/

```c++
#include <cstdio>
#include <cstring>

const int N = 101;
int M[N][N];

int grid(int n, int m) {
  if (M[n][m] != -1) return M[n][m];
  if (n == 0 || m == 0) { M[n][m] = 1; return 1; }
  return M[n][m] = grid(n - 1, m) + grid(n, m - 1);
}

int main() {
  int n, m;
  scanf("%d %d", &n, &m);
  memset(M, -1, sizeof M);
  printf("%d\n", grid(n, m));
  return 0;
}
```

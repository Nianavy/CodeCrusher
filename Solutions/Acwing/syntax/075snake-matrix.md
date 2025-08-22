https://www.acwing.com/problem/content/758/

```c++
#include <cstdio>

const int N = 110;
int n, m;
int res[N][N];
bool st[N][N];

int main() {
  scanf("%d %d", &n, &m);
  int dx[] = {0, 1, 0, -1}, dy[] = {1, 0, -1, 0};
  int x = 0, y = 0, d = 0;
  for (int i = 1; i <= n * m; ++i) {
    int nx = x + dx[d], ny = y + dy[d];
    if (nx < 0 || nx >= n || ny < 0 || ny >= m || st[nx][ny]) {
      d = (d + 1) % 4;
      nx = x + dx[d], ny = y + dy[d];
    }
    res[x][y] = i;
    st[x][y] = true;
    x = nx, y = ny;
  }
  for (int i = 0; i < n; ++i) {
    for (int j =0; j < m; ++j) printf("%d ", res[i][j]);
    puts("");
  }
  return 0;
}
```

https://www.acwing.com/problem/content/846/

### Think
```
BFS
```

### Ways
By C++
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>

typedef std::pair<int, int> PII;
const int N = 110;
int g[N][N], d[N][N], n, m;

int bfs() {
  std::queue<PII> q;
  
  memset(d, -1, sizeof d);
  d[0][0] = 0;
  q.push({0, 0});
  
  int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
  
  while (q.size()) {
    auto t = q.front();
    q.pop();
    
    for (int i = 0; i < 4; ++i) {
      int x = t.first + dx[i], y = t.second + dy[i];
      if (x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1) {
        d[x][y] = d[t.first][t.second] + 1;
        q.push({x, y});
      }
    }
  }
  return d[n - 1][m - 1];
}

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 0; i < n; ++i)
    for (int j = 0; j < m; ++j)
      scanf("%d", &g[i][j]);
  printf("%d\n", bfs());
  return 0;
}
```

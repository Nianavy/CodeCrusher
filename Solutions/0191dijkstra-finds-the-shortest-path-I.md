https://www.acwing.com/problem/content/851/

### Think
```
Dijkstra: plain version
```

### Ways
By C++
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 510;
int g[N][N], dist[N], n, m;
bool st[N];

int dijkstra() {
  memset(dist, 0x3f, sizeof dist);
  dist[1] = 0;
  for (int i = 0; i < n - 1; ++i) {
    int t = -1;
    for (int j = 1; j <= n; ++j)
      if (!st[j] && (t == -1 || dist[t] > dist[j])) t = j;
    for (int j = 1; j <= n; ++j) dist[j] = std::min(dist[j], dist[t] + g[t][j]);
    st[t] = true;
  }
  if (dist[n] == 0x3f3f3f3f) return -1;
  return dist[n];
}

int main() {
  scanf("%d%d", &n, &m);
  memset(g, 0x3f, sizeof g);
  while (m--) {
    int a, b, c;
    scanf("%d%d%d", &a, &b, &c);
    g[a][b] = std::min(g[a][b], c);
  }
  printf("%d\n", dijkstra());
  return 0;
}
```

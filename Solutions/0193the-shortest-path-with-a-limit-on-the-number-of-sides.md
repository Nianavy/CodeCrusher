https://www.acwing.com/problem/content/855/

### Think
```
bellman-ford
```

### Ways
By C++
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 510, M = 1e4 + 10;
int dist[N], last[N], n, m, k;
struct Edge {
  int a, b, c;
}edges[M];

void bellman_ford() {
  memset(dist, 0x3f, sizeof dist);
  dist[1] = 0;
  for (int i = 0; i < k; ++i) {
    memcpy(last, dist, sizeof dist);
    for (int j = 0; j < m; ++j) {
      auto e = edges[j];
      dist[e.b] = std::min(dist[e.b], last[e.a] + e.c);
    }
  }
}

int main() {
  scanf("%d%d%d", &n, &m, &k);
  for (int i = 0; i < m; ++i) {
    int a, b, c;
    scanf("%d%d%d", &a, &b, &c);
    edges[i] = {a, b, c};
  }
  bellman_ford();
  if (dist[n] > 0x3f3f3f3f / 2) puts("impossible");
  else printf("%d\n", dist[n]);
  return 0;
}
```

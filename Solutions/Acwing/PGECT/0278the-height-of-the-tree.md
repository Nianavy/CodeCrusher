https://www.acwing.com/problem/content/3702/

### Think
```
Traverse the tree via DFS,
record distance from root to each node;
the maximum is the tree depth.
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 10010, M = N * 2;
int n, root, h[N], e[M], ne[M], idx, dist[N];

void add(int a, int b) {
  e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

void dfs(int u, int fa) {
  for (int i = h[u]; i != -1; i = ne[i]) {
    int j = e[i];
    if (j == fa) continue;
    dist[j] = dist[u] + 1;
    dfs(j, u);
  }
}

int main() {
  scanf("%d %d", &n, &root);
  memset(h, -1, sizeof h);
  for (int i = 0; i < n - 1; ++i) {
    int a, b;
    scanf("%d %d", &a, &b);
    add(a, b), add(b, a);
  }
  dfs(root, -1);
  int res = 0;
  for (int i = 1; i <= n; ++i) res = std::max(res, dist[i]);
  printf("%d\n", res);
  return 0;
}
```

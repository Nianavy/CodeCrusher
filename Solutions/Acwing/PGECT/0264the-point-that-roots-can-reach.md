https://www.acwing.com/problem/content/3715/

### Think
```
binary search + DFS:
The algorithm uses binary search on edge weights combined with DFS
to find the minimum weight threshold so that the reachable subtree size from node 0,
counting only edges with weight ≥ threshold, does not exceed m.
```

### Ways
```C++
#include <cstdio>
#include <cstring>

const int N = 20010, M = N * 2;
int n, m, h[N], e[M], w[M], ne[M], idx;

void add(int, int, int);
int dfs(int, int, int);

int main() {
  int T;
  scanf("%d", &T);
  while (T--) {
    scanf("%d %d", &n, &m);
    memset(h, -1, sizeof h);
    idx = 0;
    for (int i = 0; i < n - 1; ++i) {
      int a, b, c;
      scanf("%d %d %d", &a, &b, &c);
      add(a, b, c);
      add(b, a, c);
    }
    int l = 0, r = 1e8;
    while (l < r) {
      int mid = (l + r) >> 1;
      if (dfs(0, mid, -1) <= m) r = mid;
      else l = mid + 1;
    }
    printf("%d\n", r);
  }
  return 0;
}

void add(int a, int b, int c) {
  e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}

int dfs(int u, int mid, int fa) {
  int res = 1;
  for (int i = h[u]; ~i; i = ne[i]) {
    int j = e[i];
    if (j == fa || w[i] < mid) continue;
    res += dfs(j, mid, u);
  }
  return res;
}
```

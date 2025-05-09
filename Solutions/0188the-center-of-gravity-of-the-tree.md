https://www.acwing.com/problem/content/848/

### Think
```
DFS: Depth-first traversal of trees and graphs
```

### Ways
By C++
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 1e5 + 10, M = N * 2;
int h[N], e[M], ne[M], idx, n, ans = N;
bool st[N];

void add(int a, int b) {
  e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

int dfs(int u) {
  st[u] = true;
  int size = 0, sum = 0;
  for (int i = h[u]; i != -1; i = ne[i]) {
    int j = e[i];
    if (st[j]) continue;
    int s = dfs(j);
    size = std::max(size, s);
    sum += s;
  }
  size = std::max(size, n - sum - 1);
  ans = std::min(ans, size);
  return sum + 1;
}

int main() {
  scanf("%d", &n);
  memset(h, -1, sizeof h);
  for (int i = 0; i < n - 1; ++i) {
    int a, b;
    scanf("%d%d", &a, &b);
    add(a, b), add(b, a);
  }
  dfs(1);
  printf("%d\n", ans);
  return 0;
}
```

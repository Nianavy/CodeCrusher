https://www.acwing.com/problem/content/287/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 6010;
int h[N], e[N], ne[N], idx, happy[N], f[N][2], n;
bool has_fa[N];

void add(int a, int b) {
  e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

void dfs(int u) {
  f[u][1] = happy[u];
  for (int i = h[u]; ~i; i = ne[i]) {
    int j = e[i];
    dfs(j);
    f[u][1] += f[j][0];
    f[u][0] += std::max(f[j][0], f[j][1]);
  }
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; ++i) scanf("%d", &happy[i]);
  memset(h, -1, sizeof h);
  for (int i = 0; i < n - 1; ++i) {
    int a, b;
    scanf("%d%d", &a, &b);
    add(b, a);
    has_fa[a] = true;
  }
  int root = 1;
  while (has_fa[root]) ++root;
  dfs(root);
  printf("%d\n", std::max(f[root][0], f[root][1]));
  return 0;
}
```

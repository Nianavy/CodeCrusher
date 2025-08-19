https://www.acwing.com/problem/content/3590/

```txt
Use Union-Find to check if the graph is connected, i.e.,
all nodes belong to the same connected component.
```

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 1010;
int n, m, p[N];

int find(int x) {
  if (p[x] != x) p[x] = find(p[x]);
  return p[x];
}

int main() {
  while (scanf("%d %d", &n, &m) == 2) {
    for (int i = 1; i <= n; ++i) p[i] = i;
    while (m--) {
      int a, b;
      scanf("%d %d", &a, &b);
      p[find(a)] = find(b);
    }
    bool res = true;
    for (int i = 2; i <= n; ++i) {
      if (find(i) != find(1)) {
        res = false;
        break;
      }
    }
    if (res) puts("YES");
    else puts("NO");
  }
  return 0;
}
```

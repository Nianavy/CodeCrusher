https://www.acwing.com/problem/content/849/

### Think
```
BFS: Breadth-first traversal of trees and graphs
```

### Ways
By C++
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>

const int N = 1e5 + 10;
int h[N], e[N], ne[N], d[N], idx, n, m;

void add(int a, int b) {
  e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

int bfs() {
  memset(d, -1, sizeof d);
  std::queue<int> q;
  d[1] = 0;
  q.push(1);
  while (q.size()) {
    int t = q.front();
    q.pop();
    for(int i = h[t]; i != -1; i = ne[i]) {
      int j =e[i];
      if (d[j] == -1) {
        d[j] = d[t] + 1;
        q.push(j);
      }
    }
  }
  return d[n];
}

int main() {
  scanf("%d%d", &n, &m);
  memset(h, -1, sizeof h);
  for (int i = 0; i < m; ++i) {
    int a, b;
    scanf("%d%d", &a, &b);
    add(a, b);
  }
  printf("%d\n", bfs());
  return 0;
}
```

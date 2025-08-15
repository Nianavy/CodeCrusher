https://www.acwing.com/problem/content/853/

### Think
```
SPFA
```

### Ways
By C++
```C++
#include <cstdio>
#include <queue>
#include <cstring>
#include <algorithm>

const int N = 1e5 + 10;
int dist[N], h[N], w[N], e[N], ne[N], idx, n, m;
bool st[N];

void add(int a, int b, int c) {
  e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}

int spfa() {
  memset(dist, 0x3f, sizeof dist);
  dist[1] = 0;
  
  std::queue<int> q;
  q.push(1);
  st[1] = true;
  
  while (q.size()) {
    int t = q.front();
    q.pop();
    
    st[t] = false;
    
    for (int i = h[t]; i != -1; i = ne[i]) {
      int j = e[i];
      if (dist[j] > dist[t] + w[i]) {
        dist[j] = dist[t] + w[i];
        if (!st[j]) {
          q.push(j);
          st[j] = true;
        }
      }
    }
  }
  return dist[n];
}

int main() {
  scanf("%d%d", &n, &m);
  memset(h, -1, sizeof h);
  while (m--) {
    int a, b, c;
    scanf("%d%d%d", &a, &b, &c);
    add(a, b, c);
  }
  int t = spfa();
  if (t == 0x3f3f3f3f) puts("impossible");
  else printf("%d\n", t);
  return 0;
}
```

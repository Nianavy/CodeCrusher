https://www.acwing.com/problem/content/856/

### Think
```
Floyd
```

### Ways
By C++
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 210, INF = 1e9;
int d[N][N], n, m, Q;

void floyd() {
  for (int k = 1; k <= n; ++k)
    for (int i = 1; i <= n; ++i)
      for (int j = 1; j <= n; ++j)
        d[i][j] = std::min(d[i][j], d[i][k] + d[k][j]);
}

int main() {
  scanf("%d%d%d", &n, &m, &Q);
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= n; ++j)
      if (i == j) d[i][j] = 0;
      else d[i][j] = INF;

  while (m--) {
    int a, b, c;
    scanf("%d%d%d", &a, &b, &c);
    d[a][b] = std::min(d[a][b], c);
  }
  
  floyd();
  
  while (Q--) {
    int a, b;
    scanf("%d%d", &a, &b);
    int t = d[a][b];
    if (t > INF / 2) puts("impossible");
    else printf("%d\n", t);
  }
  
  return 0;
}
```

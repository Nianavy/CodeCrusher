https://www.acwing.com/problem/content/3722/

### Think
```
Disjoint-set data structure
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N =1010;
int n, m;
int p[N];

int find(int x) {
  if (p[x] != x) p[x] = find(p[x]);
  return p[x];
}

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i) p[i] = i;
  
  int cnt = n;
  while (m--) {
    int a, b;
    scanf("%d%d", &a, &b);
    if (find(a) != find(b)) {
      p[find(a)] = find(b);
      --cnt;
    }
  }
  printf("%d\n", cnt - 1);
  return 0;
}
```

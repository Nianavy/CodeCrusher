https://www.acwing.com/problem/content/838/

### Think
```
Disjoint set
```

### Ways
By C++
```C++
#include <cstdio>

const int N = 1e5 + 10;
int p[N];

int find(int x) {
  if (p[x] != x) p[x] = find(p[x]);
  return p[x];
}

int main() {
  int n, m;
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i) p[i] = i;
  
  while (m--) {
    char op[2];
    int a, b;
    scanf("%s%d%d", op, &a, &b);
    if (*op == 'M') p[find(a)] = find(b);
    else {
      if (find(a) == find(b)) puts("Yes");
      else puts("No");
    }
  }
  
  return 0;
}
```

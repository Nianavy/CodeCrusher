https://www.acwing.com/problem/content/895/

### Think
```
Impartial Combinatorial Games
Sprague-Grundy
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <unordered_set>

const int N = 110, M = 1e4 + 10;
int s[N], f[M], n, m;

int sg(int x) {
  if (f[x] != -1) return f[x];
  std::unordered_set<int> S;
  for (int i = 0; i < m; ++i) {
    int sum = s[i];
    if (x >= sum) S.insert(sg(x - sum));
  }
  for (int i = 0; ; ++i)
    if (!S.count(i)) return f[x] = i;
}

int main() {
  scanf("%d", &m);
  for (int i = 0; i < m; ++i) scanf("%d", &s[i]);
  scanf("%d", &n);
  memset(f, -1, sizeof f);
  int res = 0;
  for (int i = 0; i < n; ++i) {
    int x;
    scanf("%d", &x);
    res ^= sg(x);
  }
  if (res) puts("Yes");
  else puts("No");
  return 0;
}
```

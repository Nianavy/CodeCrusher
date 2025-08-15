https://www.acwing.com/problem/content/896/

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

const int N = 110;
int f[N], n;

int sg(int x) {
  if (f[x] != -1) return f[x];
  std::unordered_set<int> S;
  for (int i = 0; i < x; ++i)
    for (int j = 0; j <= i; ++j) 
      S.insert(sg(i) ^ sg(j));
  for (int i = 0; ; ++i)
    if (!S.count(i)) return f[x] = i;
}

int main() {
  scanf("%d", &n);
  memset(f, -1, sizeof f);
  int res = 0;
  while (n--) {
    int x;
    scanf("%d", &x);
    res ^= sg(x);
  }
  if (res) puts("Yes");
  else puts("No");
  return 0;
}
```

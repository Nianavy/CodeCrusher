https://www.acwing.com/problem/content/293/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <vector>
#include <cstring>
#include <algorithm>

typedef long long LL;
const int N = 12, M = 1 << N;
int n, m;
LL f[N][M];
std::vector<int> state[M];
bool st[M];

int main() {
  while (scanf("%d%d", &n, &m), n || m) {
    for (int i = 0; i < 1 << n; ++i) {
      int cnt = 0;
      bool is_valid = true;
      for (int j = 0; j < n; ++j)
        if (i >> j & 1) {
          if (cnt & 1) {
            is_valid = false;
            break;
          }
          cnt = 0;
        }
        else ++cnt;
        if (cnt & 1) is_valid = false;
        st[i] = is_valid;
    }
    for (int i = 0; i < 1 << n; ++i) {
      state[i].clear();
      for (int j = 0; j < 1 << n; ++j)
        if ((i & j) == 0 && st[i | j]) state[i].push_back(j);
    }
    memset(f, 0, sizeof f);
    f[0][0] = 1;
    for (int i = 1; i <= m; ++i)
      for (int j = 0; j < 1 << n; ++j)
        for (auto k : state[j]) f[i][j] += f[i - 1][k];
    printf("%lld\n", f[m][0]);
  }
  return 0;
}
```

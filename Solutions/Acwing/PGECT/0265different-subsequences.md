https://www.acwing.com/problem/content/3716/

### Think
```
DP:
Prestore character positions to optimize traversal;
Iterate in reverse to prevent state overwrite;
Use dynamic programming to accumulate match counts;
Apply modulo to avoid overflow.
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <vector>

const int N = 10010, MOD = 1e9 + 7;
int f[N];
char a[N], b[N];

int main() {
  int T;
  scanf("%d", &T);
  
  while (T--) {
    scanf("%s%s", a + 1, b + 1);
    int n = strlen(a + 1), m = strlen(b + 1);
    
    std::vector<int> p[26];
    
    for (int i = m; i; --i) p[b[i] - 'a'].push_back(i);
    
    memset(f, 0, sizeof f);
    
    f[0] = 1;
    for (int i = 1; i <= n; ++i)
      for (int j : p[a[i] - 'a']) f[j] = (f[j] + f[j - 1]) % MOD;
      
    printf("%d\n", f[m]);
  }
  
  return 0;
}
```

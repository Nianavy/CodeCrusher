https://www.acwing.com/problem/content/3713/

### Think
```
This program counts "stepping numbers" in the range [l, r] over multiple test cases. A stepping number has adjacent digits differing by one.

It performs a DFS from digits 1-9, extending numbers by adding digits d-1 and d+1 (d = last digit). The search prunes when exceeding r and counts numbers within [l, r] of length ≥ 2.

This backtracking with pruning efficiently enumerates all valid stepping numbers in the range.
```

### Ways
```C++
#include <cstdio>

typedef long long LL;
int l, r, ans;

void dfs(LL);

int main() {
  int T;
  scanf("%d", &T);
  
  while (T--) {
    scanf("%d%d", &l, &r);
    ans = 0;
    for (int i = 1; i <= 9; ++i) dfs(i);
    printf("%d\n", ans);
  }
  
  return 0;
}

void dfs(LL x) {
  if (x > r) return;
  if (x >= l && x >= 10) ++ans;
  int d = x % 10;
  if (d) dfs(x * 10 + d - 1);
  if (d < 9) dfs(x * 10 + d + 1);
}
```

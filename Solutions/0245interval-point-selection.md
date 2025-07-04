https://www.acwing.com/problem/content/907/

### Think
```
greedy algorithm
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 1e5 + 10;
int n;
struct Range {
  int l, r;
  bool operator< (const Range &W) const {
    return r < W.r;
  }
}range[N];

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d%d", &range[i].l, &range[i].r);
  std::sort(range, range + n);
  int res = 0, ed = -2e9;
  for (int i = 0; i < n; ++i)
    if (range[i].l > ed) {
      ++res;
      ed = range[i].r;
    }
  printf("%d\n", res);
  return 0;
}
```

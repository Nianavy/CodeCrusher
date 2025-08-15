https://www.acwing.com/problem/content/127/

### Think
```
greedy algorithm
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

typedef std::pair<int, int> PII;
const int N = 5e4 + 10;
int n;
PII cow[N];

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) {
    int s, w;
    scanf("%d%d", &w, &s);
    cow[i] = {w + s, w};
  }
  std::sort(cow, cow + n);
  int res = -2e9, sum = 0;
  for (int i = 0; i < n; ++i) {
    int s = cow[i].first - cow[i].second, w = cow[i].second;
    res = std::max(res, sum - s);
    sum += w;
  }
  printf("%d\n", res);
  return 0;
}
```

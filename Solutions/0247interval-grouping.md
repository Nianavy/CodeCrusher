https://www.acwing.com/problem/content/908/

### Think
```
greedy algorithm
```

### Ways
```C++
#include <cstdio>
#include <algorithm>
#include <vector>
#include <queue>

const int N = 1e5 + 10;
int n;
struct Range {
  int l, r;
  bool operator< (const Range &W) const {
    return l < W.l;
  }
}range[N];

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) {
    int l, r;
    scanf("%d%d", &l, &r);
    range[i] = {l, r};
  }
  std::sort(range, range + n);
  std::priority_queue<int, std::vector<int>, std::greater<int>> heap;
  for (int i = 0; i < n; ++i) {
    auto r = range[i];
    if (heap.empty() || heap.top() >= r.l) heap.push(r.r);
    else {
      heap.pop();
      heap.push(r.r);
    }
  }
  printf("%d\n", heap.size());
  return 0;
}
```

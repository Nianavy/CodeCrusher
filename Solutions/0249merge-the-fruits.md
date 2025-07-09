https://www.acwing.com/problem/content/150/

### Think
```
greedy algorithm
```

### Ways
```C++
#include <cstdio>
#include <queue>
#include <vector>
#include <algorithm>

int main() {
  int n;
  scanf("%d", &n);
  std::priority_queue<int, std::vector<int>, std::greater<int>> heap;
  while (n--) {
    int x;
    scanf("%d", &x);
    heap.push(x);
  }
  int res = 0;
  while (heap.size() > 1) {
    int a = heap.top(); heap.pop();
    int b = heap.top(); heap.pop();
    res += a + b;
    heap.push(a + b);
  }
  printf("%d\n", res);
  return 0;
}
```

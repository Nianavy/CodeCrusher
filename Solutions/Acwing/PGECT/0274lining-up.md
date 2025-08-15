https://www.acwing.com/problem/content/3707/

### Think
```
Maintain in-degree counts for each node (d[]).
Use a min-heap (priority queue) to always pop the node with in-degree zero, prioritizing smaller (lexicographically earlier) nodes.
Each time a node is popped, update its neighbors’ in-degrees; if any neighbor’s in-degree drops to zero, add it to the heap.
Continue until all nodes are processed, resulting in a topological order that respects dependencies, with lex order priority.
```

### Ways
```C++
#include <cstdio>
#include <queue>
#include <cstring>
#include <algorithm>

const int N = 610, M = 5010;
int n, m, h[N], e[M], ne[M], idx, d[N];

void add(int a, int b) {
  e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

void topsort() {
  std::priority_queue<int, std::vector<int>, std::greater<int>> heap;
  for (int i = 1; i <= n; ++i)
    if (!d[i]) heap.push(i);
    
  while (heap.size()) {
    auto t = heap.top();
    printf("%d ", t);
    heap.pop();
    
    for (int i = h[t]; i != -1; i = ne[i]) {
      int j = e[i];
      if (--d[j] == 0) heap.push(j);
    }
  }
}

int main() {
  scanf("%d %d", &n, &m);
  memset(h, -1, sizeof h);
  
  while (m--) {
    int a, b;
    scanf("%d %d", &a, &b);
    d[b]++;
    add(a, b);
  }
  
  topsort();
  
  return 0;
}
```

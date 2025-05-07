https://www.acwing.com/problem/content/847/

### Think
```
BFS
```

### Ways
By C++
```C++
#include <cstdio>
#include <queue>
#include <algorithm>
#include <unordered_map>

int bfs(std::string state) {
  std::queue<std::string> q;
  std::unordered_map<std::string, int> d;
  
  q.push(state);
  d[state] = 0;
  
  int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
  std::string end = "12345678x";
  while (q.size()) {
    auto t = q.front();
    q.pop();
    if (t == end) return d[t];
    int distance = d[t];
    int k = t.find('x');
    int x = k / 3, y = k % 3;
    for (int i = 0; i < 4; ++i) {
      int a = x + dx[i], b = y + dy[i];
      if (a >= 0 && a < 3 && b >= 0 && b < 3) {
        std::swap(t[a * 3 + b], t[k]);
        if (!d.count(t)) {
          d[t] = distance + 1;
          q.push(t);
        }
        std::swap(t[a * 3 + b], t[k]);
      }
    }
  }
  return -1;
}

int main() {
  char s[2];
  std::string state;
  for (int i = 0; i < 9; ++i) {
    scanf("%s", s);
    state += *s;
  }
  printf("%d\n", bfs(state));
  return 0;
}
```

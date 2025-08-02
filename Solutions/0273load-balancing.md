https://www.acwing.com/problem/content/5074/

### Think
```
Recursively split a number into multiple parts,
tracking the maximum count and
minimal difference between max and min parts.
At each step,
split the two largest parts,
exploring all partitions.
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 210;
int n, w[N], cnt, m, d;

int get() {
  int x = 0, y = N;
  for (int i = 0; i < cnt; ++i) {
    x = std::max(x, w[i]);
    y = std::min(y, w[i]);
  }
  return x - y;
}

void dfs() {
  if (cnt > m) m = cnt, d = get();
  else if (cnt == m) d = std::min(d, get());
  
  if (cnt == 1) {
    int a = w[0];
    for (int x = a / 3 + 1; x <= a / 2; ++x) {
      w[0] = x, w[cnt++] = a - x;
      dfs();
      w[0] = a, cnt--;
    }
  }
  else {
    int i = 0, j = -1;
    for (int k = 1; k < cnt; ++k)
      if (w[k] >= w[i]) j = i, i = k;
      else if (j == -1 || w[k] > w[j]) j = k;
    
    int a = w[i], b = w[j];
    if (a > b) {
      for (int x = std::max(b / 2, a / 3) + 1; x <= a / 2; ++x) {
        w[i] = x, w[cnt++] = a - x;
        dfs();
        w[i] = a, cnt--;
      }
    }
  }
}

int main() {
  scanf("%d", &n);
  w[cnt++] = n;
  dfs();
  printf("%d %d\n", m, d);
  return 0;
}
```

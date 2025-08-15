https://www.acwing.com/problem/content/5076/

```txt
This program simulates the Best Fit memory allocation algorithm
using a circular doubly linked list,
allocates memory blocks based on requested sizes,
and outputs the remaining blocks.
```

```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

const int N = 110;
int n, l[N], r[N], pos[N], len[N];

void remove(int p) {
  l[r[p]] = l[p];
  r[l[p]] = r[p];
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; ++i) {
    scanf("%d %d", &pos[i], &len[i]);
    l[i] = i - 1, r[i] = i + 1;
  }
  
  l[0] = n, r[0] = 1, r[n] = 0;
  
  int cur = 1;
  while (true) {
    int x;
    scanf("%d", &x);
    if (x == -1) break;
    
    int p = -1;
    for (int i = cur; ; i = r[i]) {
      if (len[i] >= x && (p == -1 || len[i] < len[p])) p = i;
      if (r[i] == cur) break;
    }
   
    if (p != -1) {
      if (len[p] == x) {
        cur = r[p];
        remove(p);
      }
      else {
        len[p] -= x;
        cur = p;
      }
    }
  }
  
  for (int i = cur; ; i = r[i]) {
    if (i) printf("%d %d\n", pos[i], len[i]);
    if (r[i] == cur) break;
  }
  
  return 0;
}
```

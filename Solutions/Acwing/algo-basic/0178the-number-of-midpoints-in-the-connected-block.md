https://www.acwing.com/problem/content/839/

### Think
```
Disjoint set union
```

### Ways
By C++
```C++
#include <iostream>
using namespace std;

const int N = 1e5 + 10;
int n, m, p[N], cnt[N];

int find(int x) {
  if (p[x] != x) p[x] = find(p[x]);
  return p[x];
}

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; ++i) {
    p[i] = i;
    cnt[i] = 1;
  }
  
  while (m--) {
    
    char op[5];
    
    int a, b;
    
    scanf("%s", op);
    
    if (op[0] == 'C') {
      scanf("%d%d", &a, &b);
      a = find(a), b = find(b);
      if (a != b) {
        p[a] = b;
        cnt[b] += cnt[a];
      }
    }
    else if (op[0] == 'Q' && op[1] == '1') {
      scanf("%d%d", &a, &b);
      if (find(a) == find(b)) puts("Yes");
      else puts("No");
    }
    else {
      scanf("%d", &a);
      printf("%d\n", cnt[find(a)]);
    }
  }
  
  return 0;
}
```

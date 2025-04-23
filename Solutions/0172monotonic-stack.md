https://www.acwing.com/problem/content/832/

### Think
```
monotonic stack
```

### Ways
By C++
```C++
#include <cstdio>

const int N = 1e5 + 10;
int n, t, stk[N];

int main() {
  scanf("%d", &n);
  while (n--) {
    int x;
    scanf("%d", &x);
    while (t && stk[t] >= x) --t;
    if (t) printf("%d ", stk[t]);
    else printf("-1 ");
    stk[++t] = x;
  }
  return 0;
}
```

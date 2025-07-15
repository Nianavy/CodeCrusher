https://www.acwing.com/problem/content/3720/

### Think
```
Factorization
```

### Ways
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cmath>

int main() {
  int n;
  scanf("%d", &n);
  n *= 2;
  
  int cnt = 0;
  for (int x = sqrt(n); x > 1; --x) {
    if (n % x == 0) {
      int y = n / x;
      int k = x;
      int t = y - (x - 1);
      if (t % 2 == 0) {
        int a = t / 2;
        for (int j = a; j < a + k; ++j) printf("%d ", j);
        puts("");
        ++cnt;
      } 
    }
  }
  if (!cnt) puts("NONE");
  return 0;
}
```

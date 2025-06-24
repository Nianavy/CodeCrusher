https://www.acwing.com/problem/content/898/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 100010;
int q[N], a[N], n;

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  
  int len = 0;
  for (int i = 0; i < n; ++i) {
    int l = 0, r = len;
    while (l < r) {
      int mid = l + r + 1 >> 1;
      if (q[mid] < a[i]) l = mid;
      else r = mid - 1;
    }
    len = std::max(len, r + 1);
    q[r + 1] = a[i];
  }
  printf("%d\n", len);
  return 0;
}
```

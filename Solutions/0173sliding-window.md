https://www.acwing.com/problem/content/156/

### Think
```
Monotonic queue
```

### Ways
By C++
```C++
#include <iostream>
using namespace std;
const int N = 1e6 + 10;
int a[N], q[N];
int main() {
  int n, k;
  scanf("%d%d", &n, &k);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  int h = 0, t = -1;
  for (int i = 0; i < n; ++i) {
    if (h <= t && i - k + 1 > q[h]) h++;
    while (h <= t && a[q[t]] >= a[i]) t--;
    q[++t] = i;
    if (i >= k - 1) printf("%d ", a[q[h]]);
  }
  puts("");
  h = 0, t = -1;
  for (int i = 0; i < n; ++i) {
    if (h <= t && i - k + 1 > q[h]) h++;
    while (h <= t && a[q[t]] <= a[i]) t--;
    q[++t] = i;
    if (i >= k - 1) printf("%d ", a[q[h]]);
  }
  puts("");
  return 0;
}
```

https://www.acwing.com/problem/content/802/

### Think
```
Double-Points
```

### Ways
By C++
```C++
#include <iostream>
using namespace std;
const int N = 1e5 + 10;
int n, m, x, a[N], b[N];

int main() {
  scanf("%d%d%d", &n, &m, &x);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  for (int i = 0; i < m; ++i) scanf("%d", &b[i]);
  for (int i = 0, j = m - 1; i < n; ++i) {
    while (j >= 0 && a[i] + b[j] > x) --j;
    if (j >= 0 && a[i] + b[j] == x) cout << i << ' ' << j << endl;
  }
  return 0;
}
```

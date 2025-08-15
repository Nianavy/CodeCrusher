https://www.acwing.com/problem/content/801/

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
int n, q[N], s[N];
int main() {
  scanf("%d", &n);
  for (int i = 0; i <= n; ++i) scanf("%dd", &q[i]);
  
  int res = 0;
  for (int i = 0, j = 0; i < n; ++i) {
    s[q[i]]++;
    while (j < i && s[q[i]] > 1) s[q[j++]]--;
    res = max(res, i - j + 1);
  }
  cout << res << endl;
  return 0;
}
```

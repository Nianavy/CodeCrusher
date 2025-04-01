https://www.acwing.com/problem/content/791/

### Think
```
binary-search
```

### Ways
```C++
#include <iostream>
using namespace std;

const int N = 1e6 + 10;
int t[N];

int binary_search_l(int q[], int l, int r, int x) {
  while (l < r) {
    int mid = (l + r) >> 1;
    if (q[mid] >= x) r = mid;
    else l = mid + 1;
  }
  return l;
}
int binary_search_r(int q[], int l, int r, int x) {
  while (l < r) {
    int mid = (l + r + 1) >> 1;
    if (q[mid] <= x) l = mid;
    else r = mid - 1;
  }
  return l;
}

int main() {
  int n, q;
  scanf("%d %d", &n, &q);
  for (int i = 0; i < n; ++i) scanf("%d", &t[i]);
  while (q--) {
    int k;
    scanf("%d", &k);
    int pos = binary_search_l(t, 0, n - 1, k);
    if (t[pos] != k) printf("-1 -1\n");
    else {
      printf("%d ", pos);
      pos = binary_search_r(t, 0, n - 1, k);
      printf("%d\n", pos);
    }
  }
  return 0;
}
```

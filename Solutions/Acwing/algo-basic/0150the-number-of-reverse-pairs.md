https://www.acwing.com/problem/content/790/

### Think
```
Merge-sort
```

### Ways
By C++
```C++
#include <iostream>
using namespace std;

const int N = 1e6 + 10;
int n, q[N], t[N];

long long merge_sort(int q[], int l, int r) {
  if (l >= r) return 0;
  int mid = (l + r) >> 1;
  long long ans = merge_sort(q, l, mid) + merge_sort(q, mid + 1, r);
  int k = 0, i = l, j = mid + 1;
  while (i <= mid && j <= r) {
    if (q[i] <= q[j]) t[k++] = q[i++];
    else {
      ans += mid - i + 1;
      t[k++] = q[j++];
    }
  }
  while (i <= mid) t[k++] = q[i++];
  while (j <= r) t[k++] = q[j++];
  for (int i = l, j = 0; i <= r; ++i, ++j) q[i] = t[j];
  return ans;
}

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &q[i]);
  printf("%lld\n", merge_sort(q, 0, n - 1));
  return 0;
}
```

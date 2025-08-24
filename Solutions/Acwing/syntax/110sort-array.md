https://www.acwing.com/problem/content/820/

```c++
#include <cstdio>

const int N = 10001;
int a[N];

void sort_(int q[], int l, int r) {
  if (l >= r) return;
  int v = q[(l + r) >> 1], i = l - 1, j = r + 1;
  while (i < j) {
    do ++i; while (q[i] < v);
    do --j; while (q[j] > v);
    if (i < j) {
      int t = q[i];
      q[i] = q[j];
      q[j] = t;
    }
  }
  sort_(q, l, j);
  sort_(q, j + 1, r);
}

int main() {
  int n, l, r;
  scanf("%d %d %d", &n, &l, &r);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  sort_(a, l, r);
  for (int i = 0; i < n; ++i) printf("%d ", a[i]);
  return 0;
}
```

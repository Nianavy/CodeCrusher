https://www.acwing.com/problem/content/3718/

### Think
```
During merge in merge sort,
if a right-side element is smaller than a left-side element,
all remaining elements in the left array form inversions with that right element.
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

const int N = 1010;
int n, w[N], ans[N], q[N];

void merge_sort(int, int);

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &w[i]);
  
  merge_sort(0, n - 1);
  
  int sum = 0;
  
  for (int i = 1; i <= n; ++i) {
    printf("%d ", ans[i]);
    sum += ans[i];
  }
  
  printf("\n%d\n", sum);
  
  return 0;
}

void merge_sort(int l, int r) {
  if (l >= r) return;
  int mid = (l + r) >> 1;
  merge_sort(l, mid), merge_sort(mid + 1, r);
  int i = l, j = mid + 1, k = 0;
  while (i <= mid && j <= r)
    if (w[i] <= w[j]) q[k++] = w[i++];
    else {
      q[k++] = w[j];
      ans[w[j]] += mid - i + 1;
      ++j;
    }
  while (i <= mid) q[k++] = w[i++];
  while (j <= r) q[k++] = w[j++];
  for (int i = l, j = 0; j < k; ++i, ++j) w[i] = q[j];
}
```

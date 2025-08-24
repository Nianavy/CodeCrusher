https://www.acwing.com/problem/content/818/

```c++
#include <cstdio>

const int N = 10001;

void reverse(int a[], int size) {
  for (int i = 0; i < size / 2; ++i) {
    int t = a[i];
    a[i] = a[size - 1 - i];
    a[size - 1 - i] = t;
  }
}

int main() {
  int a[N], n, size;
  scanf("%d %d", &n, &size);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  reverse(a, size);
  for (int i = 0; i < n; ++i) printf("%d ", a[i]);
  return 0;
}
```

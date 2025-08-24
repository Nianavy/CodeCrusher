https://www.acwing.com/problem/content/816/

```c++
#include <cstdio>

const int LEN = 101;

void copy(int a[], int b[], int size) {
  for (int i = 0; i < size; ++i)
    b[i] = a[i];
}

int main() {
  int a, b, size;
  scanf("%d %d %d", &a, &b, &size);
  int arr_a[LEN], arr_b[LEN];
  for (int i = 0; i < a; ++i) scanf("%d", &arr_a[i]);
  for (int i = 0; i < b; ++i) scanf("%d", &arr_b[i]);
  copy(arr_a, arr_b, size);
  for (int i = 0; i < b; ++i) printf("%d ", arr_b[i]);
  return 0;
}
```

https://www.acwing.com/problem/content/814/

```c++
#include <cstdio>

const int LEN = 1001;

void print(int a[], int size) {
  for (int i = 0; i < size; ++i) printf("%d ", a[i]);
  puts("");
}

int main() {
  int n, size;
  scanf("%d %d", &n, &size);
  int a[LEN] = {0};
  for (int i = 0; i < size; ++i) scanf("%d", &a[i]);
  print(a, size);
  return 0;
}
```

https://www.acwing.com/problem/content/819/

```c++
#include <cstdio>

const int N = 10001;
bool marked[N];

int get_unique_count(int a[], int n) {
  int ans = 0;
  for (int i = 0; i < n; ++i) {
    int curr = a[i];
    if (curr >= 0 && curr < N) {
      if (!marked[curr]) {
        marked[curr] = true;
        ++ans;
      }
    }
  }
  return ans;
}

int main() {
  int n, a[N];
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  printf("%d\n", get_unique_count(a, n));
  return 0;
}
```

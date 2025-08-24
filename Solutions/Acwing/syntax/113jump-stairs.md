https://www.acwing.com/problem/content/823/

```c++
#include <cstdio>
#include <cstring>

const int N = 1000;
int m[N];

int sol(int n) {
  if (m[n] != -1) return m[n];
  if (n < 2) {
    m[n] = 1;
    return 1;
  }
  m[n] = sol(n - 1) + sol(n - 2);
  return m[n];
}

int main() {
  int n;
  scanf("%d", &n);
  memset(m, -1, sizeof m);
  printf("%d\n", sol(n));
  return 0;
}
```

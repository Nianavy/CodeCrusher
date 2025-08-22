https://www.acwing.com/problem/content/743/

```c++
#include <cstdio>

int main() {
  long long f[60];
  f[0] = 0, f[1] = 1;
  for (int i = 2; i < 61; ++i) f[i] = f[i - 1] + f[i - 2];
  int t;
  scanf("%d", &t);
  while (t--) {
    int n;
    scanf("%d", &n);
    printf("Fib(%d) = %lld\n", n, f[n]);
  }
  return 0;
}
```

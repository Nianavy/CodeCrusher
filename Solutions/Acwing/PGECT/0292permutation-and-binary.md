https://www.acwing.com/problem/content/3591/

```txt
uses Legendre's formula to count prime factors in factorials.
```

```c++
#include <cstdio>
#include <algorithm>

int fact_prime_exp(int n, int p) {
  int res = 0;
  while (n) res += n / p, n /= p;
  return res;
}

int main() {
  int n, m;
  while (scanf("%d %d", &n, &m) == 2 && n)
    printf("%d\n", fact_prime_exp(n ,2) - fact_prime_exp(n - m, 2));
  return 0;
}
```

https://www.acwing.com/problem/content/3704/

### Think
```
Sieve of Eratosthenes(Linear Sieve)
```

### Ways
```C++
#include <cstdio>
#include <cstring>

const int N = 1e7 + 10;
int primes[N], s[N], cnt = 0;
bool st[N];

void get_primes(int);

int main() {
  get_primes(N - 1);
  int a, b;
  while (scanf("%d %d", &a, &b) == 2) printf("%d\n", s[b] - s[a - 1]);
  return 0;
}

void get_primes(int n) {
  for (int i = 2; i <= n; ++i) {
    if (!st[i]) primes[cnt++] = i;
    for (int j = 0; primes[j] * i <= n; ++j) {
      st[primes[j] * i] = true;
      if (i % primes[j] == 0) break;
    }
  }
  for (int i = 2; i <= n; ++i) {
    s[i] = s[i - 1];
    if (st[i]) s[i]++;
  }
}
```

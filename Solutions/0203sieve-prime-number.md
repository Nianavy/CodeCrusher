https://www.acwing.com/problem/content/870/

### Think
```
Sieve prime number
```

### Ways
By C++
#### Naive sieve method
```C++
#include <cstdio>

const int N = 1e6 + 10;
int primes[N], cnt;
bool st[N];

void get_primes(int n) {
  for (int i = 2; i <= n; ++i) {
    if (st[i]) continue;
    primes[cnt++] = i;
    for (int j = i + i; j <= n; j += i) st[j] = true;
  }
}

int main() {
  int n;
  scanf("%d", &n);
  get_primes(n);
  printf("%d\n", cnt);
  return 0;
}
```

#### Linear sieve method
```C++
#include <cstdio>

const int N = 1e6 + 10;
int primes[N], cnt;
bool st[N];

void get_primes(int n) {
  for (int i = 2; i <= n; ++i) {
    if (!st[i]) primes[cnt++] = i;
    for (int j = 0; primes[j] <= n / i; ++j) {
      st[primes[j] * i] = true;
      if (i % primes[j] == 0) break;
    }
  }
}

int main() {
  int n;
  scanf("%d", &n);
  get_primes(n);
  printf("%d\n", cnt);
  return 0;
}
```

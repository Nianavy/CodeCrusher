https://www.acwing.com/problem/content/876/

### Think
```
Find the Euler function by the sieve method
```

### Ways
By C++
```C++
#include <cstdio>

typedef long long LL;
const int N = 1e6 + 10;
int euler[N], primes[N], cnt;
bool st[N];

void get_euler(int n) {
  euler[1] = 1;
  for (int i = 2; i <= n; ++i) {
    if (!st[i]) {
      primes[cnt++] = i;
      euler[i] = i - 1;
    }
    for (int j = 0; primes[j] <= n / i; ++j) {
      int t = primes[j] * i;
      st[t] = true;
      if (i % primes[j] == 0) {
        euler[t] = euler[i] * primes[j];
        break;
      }
      euler[t] = euler[i] * (primes[j] - 1);
    }
  }
}

int main() {
  int n;
  scanf("%d", &n);
  get_euler(n);
  LL res = 0;
  for (int i = 1; i <= n; ++i) res += euler[i];
  printf("%ld\n", res);
  return 0;
}
```

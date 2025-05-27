https://www.acwing.com/problem/content/873/

### Think
```
divisor
```

### Ways
By C++
```C++
#include <cstdio>
#include <vector>
#include <algorithm>
#include <unordered_map>

typedef long long LL;
const int mod = 1e9 + 7, N = 110;
std::unordered_map<int, int> primes;

void calc_divisors(int x) {
  for (int i = 2; i <= x / i; ++i)
    while (x % i == 0) {
      x /= i;
      primes[i]++;
    }
  if (x > 1) primes[x]++;
}

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int x;
    scanf("%d", &x);
    calc_divisors(x);
  }
  LL res = 1;
  for (auto p : primes) {
    LL a = p.first, b = p.second;
    LL t = 1;
    while (b--) t = (t * a + 1) % mod;
    res = res * t % mod;
  }
  printf("%d\n", res);
  return 0;
}
```

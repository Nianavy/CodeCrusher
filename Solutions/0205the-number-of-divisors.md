https://www.acwing.com/problem/content/872/

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
const int mod = 1e9 + 7;
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
  for (auto p : primes) res = res * (p.second + 1) % mod;
  printf("%d\n", res);
  return 0;
}
```

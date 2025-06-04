https://www.acwing.com/problem/content/206/

### Think
```
chinese remainder theorem
```

### Ways
By C++
```C++
#include <cstdio>
#include <algorithm>

typedef long long LL;

LL exgcd(LL a, LL b, LL &x, LL &y) {
  if (!b) {
    x = 1, y = 0;
    return a;
  }
  LL d = exgcd(b, a % b, y, x);
  y -= a / b * x;
  return d;
}

int main() {
  int n;
  scanf("%d", &n);
  LL x = 0, m1, a1;
  scanf("%ld %ld", &m1, &a1);
  for (int i = 0; i < n - 1; ++i) {
    LL m2, a2;
    scanf("%ld %ld", &m2, &a2);
    LL k1, k2;
    LL d = exgcd(m1, m2, k1, k2);
    if ((a2 - a1) % d) {
      x = -1;
      break;
    }
    k1 *= (a2 - a1) / d;
    k1 = (k1 % (m2 / d) + m2 / d) % (m2 / d);
    x = k1 * m1 + a1;
    LL m = std::abs(m1 / d * m2);
    a1 = k1 * m1 + a1;
    m1 = m;
  }
  if (x != -1) x = (a1 % m1 + m1) % m1;
  printf("%ld\n", x);
  return 0;
}
```

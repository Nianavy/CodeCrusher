https://www.acwing.com/problem/content/878/

### Think
```
Fast exponentiation
```

### Ways
By C++
```C++
#include <cstdio>

typedef long long LL;

LL qmi(int a, int b, int p) {
  LL res = 1;
  while (b) {
    if (b & 1) res = res * a % p;
    a = a * (LL)a % p;
    b >>= 1;
  }
  return res;
}

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int a, p;
    scanf("%d%d", &a, &p);
    if (a % p == 0) puts("impossible");
    else printf("%lld\n", qmi(a, p - 2, p));
  }
  return 0;
}
```

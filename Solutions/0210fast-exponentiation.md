https://www.acwing.com/problem/content/877/

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
  LL res = 1 % p;
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
    int a, b, p;
    scanf("%d%d%d", &a, &b, &p);
    printf("%lld\n", qmi(a, b, p));
  }
  return 0;
}
```

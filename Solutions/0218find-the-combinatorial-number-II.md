https://www.acwing.com/problem/content/888/

### Think
```
Factorial inverse element
```

### Ways
By C++
```C++
#include <cstdio>

typedef long long LL;
const int N = 1e5 + 10, mod = 1e9 + 7;
int fact[N], infact[N];

int qmi(int a, int k, int p) {
  int res = 1;
  while (k) {
    if (k & 1) res = (LL)res * a % p;
    a = (LL)a * a % p;
    k >>= 1;
  }
  return res;
}

int main() {
  fact[0] = infact[0] = 1;
  for (int i = 1; i < N; ++i) {
    fact[i] = (LL)fact[i - 1] * i % mod;
    infact[i] = (LL)infact[i - 1] * qmi(i, mod - 2, mod) % mod;
  }
  int n;
  scanf("%d", &n);
  while (n--) {
    int a, b;
    scanf("%d%d", &a, &b);
    printf("%d\n", (LL)fact[a] * infact[b] % mod * infact[a - b] % mod);
  }
  return 0;
}
```

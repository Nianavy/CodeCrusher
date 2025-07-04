https://www.acwing.com/problem/content/890/

### Think
```
Legendre's Formula
```

### Ways
By C++
```C++
#include <cstdio>
#include <vector>

const int N = 5010;
int sum[N], primes[N], cnt;
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

int get(int n, int p) {
  int res = 0;
  while (n) {
    res += n / p;
    n /= p;
  }
  return res;
}

std::vector<int> mul(std::vector<int> a, int b) {
  std::vector<int> c;
  int t = 0;
  for (int i = 0; i < a.size(); ++i) {
    t += a[i] * b;
    c.push_back(t % 10);
    t /= 10;
  }
  while (t) {
    c.push_back(t % 10);
    t /= 10;
  }
  return c;
}

int main() {
  int a, b;
  scanf("%d%d", &a, &b);
  get_primes(a);
  for (int i = 0; i < cnt; ++i) {
    int p = primes[i];
    sum[i] = get(a, p) - get(a - b, p) - get(b, p);
  }
  std::vector<int> res;
  res.push_back(1);
  for (int i = 0; i < cnt; ++i)
    for (int j = 0; j < sum[i]; ++j)
      res = mul(res, primes[i]);
  for (int i = res.size() - 1; i >= 0; --i) printf("%d", res[i]);
  puts("");
  return 0;
}
```

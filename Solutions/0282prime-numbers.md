https://www.acwing.com/problem/content/3529/

### Think
```
Enumerate numbers like 1, 11, 111, ...,
and print those less than n that are prime.
```

### Ways
```C++
#include <cstdio>

bool is_prime(int);

int main() {
  int n;
  while (scanf("%d", &n) == 1) {
    int cnt = 0;
    for (int i = 1; i < n; i = (i / 10 + 1) * 10 + 1)
      if (is_prime(i)) {
        ++cnt;
        printf("%d ", i);
      }
    if (!cnt) printf("-1");
    printf("\n");
  }
  return 0;
}

bool is_prime(int x) {
  if (x < 2) return false;
  for (int i = 2; i <= x / i; ++i)
    if (x % i == 0) return false;
  return true;
}
```

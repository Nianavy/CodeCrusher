https://www.acwing.com/problem/content/728/

```c++
#include <cstdio>

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int x;
    scanf("%d", &x);
    bool is_prime = true;
    for (int i = 2; i <= x / i; ++i) {
      if (x % i == 0) {
        is_prime = false;
        break;
      }
    }
    if (is_prime) printf("%d is prime\n", x);
    else printf("%d is not prime\n", x);
  }
  return 0;
}
```

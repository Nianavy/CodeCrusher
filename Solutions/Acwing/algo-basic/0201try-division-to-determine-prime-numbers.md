https://www.acwing.com/problem/content/868/

### Think
```
Try division to determine prime numbers
```

### Ways
By C++
```C++
#include <cstdio>

bool is_prime(int x) {
  if (x < 2) return false;
  for (int i = 2; i <= x / i; ++i)
    if (x % i == 0) return false;
  return true;
}

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int x;
    scanf("%d", &x);
    if (is_prime(x)) puts("Yes");
    else puts("No");
  }
  return 0;
}
```

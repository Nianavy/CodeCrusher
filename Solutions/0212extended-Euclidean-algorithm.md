https://www.acwing.com/problem/content/879/

### Think
```
extended Euclidean algorithm
```

### Ways
By C++
```C++
#include <cstdio>

int exgcd(int a, int b, int &x, int &y) {
  if (!b) {
    x = 1, y = 0;
    return a;
  }
  int d = exgcd(b, a % b, y, x);
  y -= a / b * x;
  return d;
}

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int a, b;
    scanf("%d%d", &a, &b);
    int x, y;
    exgcd(a, b, x, y);
    printf("%d %d\n", x, y);
  }
  return 0;
}
```

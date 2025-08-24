https://www.acwing.com/problem/content/811/

```c++
#include <cstdio>

int gcd(int a, int b) {
  if (b == 0) return a;
  else return gcd(b, a % b);
}

int lcm(int a, int b) {
  return (a / gcd(a, b)) * b;
}

int main() {
  int a, b;
  scanf("%d %d", &a, &b);
  printf("%d\n", lcm(a, b));
  return 0;
}
```

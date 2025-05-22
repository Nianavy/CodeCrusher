https://www.acwing.com/problem/content/869/

### Think
```
Decompose prime factors
```

### Ways
By C++
```C++
#include <cstdio>

void divide(int x) {
  for (int i = 2; i <= x / i; ++i)
    if (x % i == 0) {
      int s = 0;
      while (x % i == 0) x /= i, ++s;
      printf("%d %d\n", i, s);
    }
    if (x > 1) printf("%d %d\n", x, 1);
    puts("");
}

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int x;
    scanf("%d", &x);
    divide(x);
  }
  return 0;
}
```

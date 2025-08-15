https://www.acwing.com/problem/content/3645/

### Think
```
Calculate the greatest common divisor (GCD) of the two numbers using the recursive Euclidean algorithm.  
Compute the least common multiple (LCM) as a * b / d.
```

### Ways
```C++
#include <cstdio>

int gcd(int, int);

int main() {
  int a, b;
  scanf("%d %d", &a, &b);
  int d = gcd(a, b);
  printf("%d %d\n", d, a * b / d);
  return 0;
}

int gcd(int a, int b) {
  return b ? gcd(b, a % b) : a;
}
```

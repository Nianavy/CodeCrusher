https://www.acwing.com/problem/content/3593/

```txt
Compute the sum of digits of a number and the sum of digits of its square.
```

```c++
#include <cstdio>

int get(int x) {
  int sum = 0;
  while (x) sum += x % 10, x /= 10;
  return sum;
}

int main() {
  int n;
  while (scanf("%d", &n) == 1)
    printf("%d %d\n", get(n), get(n * n));
  return 0; 
}
```

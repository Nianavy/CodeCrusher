https://www.acwing.com/problem/content/5071/

### Think
```
Enumerate b and check if the sequence func(b+i) for i in [0, n-1]
forms an arithmetic progression with difference 1;
output all (a, b) pairs that satisfy.
```

### Ways
```C++
#include <cstdio>
#include <cmath>

int func(int);

int main() {
  int n, ma;
  scanf("%d %d", &n, &ma);
  int mb = sqrt((ma + n - 1) * 1000 + 999);
  
  int cnt = 0;
  for (int b = 32; b <= mb; ++b) {
    int a = func(b);
    bool success = true;
    for (int i = 1; i < n; ++i)
      if (a + i != func(b + i)) {
        success = false;
        break;
      }
    if (success) {
      printf("%d %d\n", a, b);
      ++cnt;
    }
  }
  
  if (!cnt) puts("No Solution.");
  
  return 0;
}

int func(int b) {
  return b * b / 1000;
}
```

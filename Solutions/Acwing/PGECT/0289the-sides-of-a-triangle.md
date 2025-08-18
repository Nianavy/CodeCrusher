https://www.acwing.com/problem/content/3588/

```txt
The core idea is to sort the three numbers to identify the smallest,
second-smallest, and largest,
then directly compute the result of "smallest + second-smallest - largest".
```

```c++
#include <cstdio>
#include <algorithm>

int main() {
  int a[3];
  while (scanf("%d %d %d", &a[0], &a[1], &a[2]) == 3) {
    std::sort(a, a + 3);
    printf("%d\n", a[0] + a[1] - a[2]);
  }
  return 0;
}
```

https://www.acwing.com/problem/content/755/

```c++
#include <cstdio>

int max_(int a, int b) {
    return (a > b) ? a : b;
}

int min_(int a, int b) {
    return (a < b) ? a : b;
}

int abs_(int x) {
  return x > 0 ? x : -x;
}

int main() {
    int n, a[100][100];

    while (scanf("%d", &n) == 1 && n != 0) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (n % 2) {
                    a[i][j] = (n + 1) / 2 - max_(abs_(i - n / 2), abs_(j - n / 2));
                } else {
                    a[i][j] = n / 2 - max_(min_(abs_(i - n / 2), abs_(i - (n - 1) / 2)), min_(abs_(j - n / 2), abs_(j - (n - 1) / 2)));
                }
            }
        }

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                printf("%d ", a[i][j]);
            }
            printf("\n");
        }
        printf("\n");
    }

    return 0;
}
```

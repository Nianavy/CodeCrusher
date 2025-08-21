https://www.acwing.com/problem/content/655/

```c++
#include <cstdio>

int main() {
    int n;
    scanf("%d", &n);

    int denominations[] = {100, 50, 20, 10, 5, 2, 1};
    int count, i;
    printf("%d\n",n);
    for (i = 0; i < 7; i++) {
        count = n / denominations[i];
        n -= count * denominations[i];
        printf("%d nota(s) de R$ %d,00\n", count, denominations[i]);
    }

    return 0;
}
```

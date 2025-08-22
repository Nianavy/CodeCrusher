https://www.acwing.com/problem/content/720/

```c++
#include <cstdio>

int main() {
    int total=0, n;
    int sr = 0, sf=0, sc = 0;
    
    scanf("%d", &n);

    while(n--)
    {
        int a;
        char c;
        scanf("%d %c", &a, &c);
        if (c == 'C')
            sc += a;
        else if (c=='R')
            sr += a;
        else
            sf += a;
        total += a;

    }
    printf("Total: %d animals\n", total);
    printf("Total coneys: %d\n", sc);
    printf("Total rats: %d\n", sr);
    printf("Total frogs: %d\n", sf);
    printf("Percentage of coneys: %.2lf %%\n", sc*100.0/total);
    printf("Percentage of rats: %.2lf %%\n", sr*100.0/total);
    printf("Percentage of frogs: %.2lf %%\n", sf*100.0/total);

    return 0;
}
```

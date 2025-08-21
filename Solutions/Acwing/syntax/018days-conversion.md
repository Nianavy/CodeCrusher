https://www.acwing.com/problem/content/657/

```c++
#include <cstdio>

int main()
{
    int n;
    scanf("%d",&n);

    int y = n / 365;
    int m = n % 365 / 30;
    int d = n % 365 % 30; 

    printf("%d ano(s)\n%d mes(es)\n%d dia(s)\n",y,m,d);

    return 0;
}
```

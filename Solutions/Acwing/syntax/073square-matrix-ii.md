https://www.acwing.com/problem/content/756/

```c++
#include <cstdio>

int main()
{
    int n,a[100][100];
    while(scanf("%d", &n), n>0)
    {
        for(int i=0;i<n;i++)
        {
            for(int j=i,k=1;j<n;j++,k++)
                a[i][j]=k;
            for(int j=i,k=1;j<n;j++,k++)
                a[j][i]=k;
        }
        for(int i=0;i<n;i++)
        {
            for(int j=0;j<n;j++)
                printf("%d ", a[i][j]);
            puts("");
        }
        puts("");
    }
    return 0;
}
```

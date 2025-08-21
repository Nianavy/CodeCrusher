https://www.acwing.com/problem/content/656/

```c++
#include <cstdio>

int main(){
    int n;
    scanf("%d",&n);
    int h = n/3600;
    int m = n%3600/60;
    int s = n%60;
    printf("%d:%d:%d\n",h,m,s);
    return 0;
}
```

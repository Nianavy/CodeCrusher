https://www.acwing.com/problem/content/3725/

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1010;

int n;
int h[N];

int main()
{
    while (scanf("%d", &n) != -1)
    {
        for (int i = 0; i < n; i ++ ) scanf("%d", &h[i]);

        int res = 0;
        for (int i = 0; i < n;)
        {
            int j = i + 1;
            while (j < n && h[j] >= h[j - 1]) j ++ ;
            res = max(res, h[j - 1] - h[i]);
            i = j;
        }

        printf("%d\n", res);
    }

    return 0;
}
```

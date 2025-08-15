https://www.acwing.com/problem/content/2818/

### Think
```
Double-Points
```

### Ways
By C++
```C++
#include <iostream>
#include <cstring>
using namespace std;
const int N = 1e5 + 10;
int n, m, a[N], b[N];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i ++ ) scanf("%d", &a[i]);
    for (int i = 0; i < m; i ++ ) scanf("%d", &b[i]);
    int i = 0, j = 0;
    while (i < n && j < m) {
        if (a[i] == b[j]) i ++ ;
        j ++ ;
    }
    if (i == n) puts("Yes");
    else puts("No");

    return 0;
}
```

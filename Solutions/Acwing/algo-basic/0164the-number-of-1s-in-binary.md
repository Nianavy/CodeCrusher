https://www.acwing.com/problem/content/803/

### Think
```
bit-operation
```

### Ways
By C++
```C++
#include <cstdio>

int main() {
    int n;
    scanf("%d", &n);
    while (n--) {
        int x, s = 0;
        scanf("%d", &x);
        for (int i = x; i; i -= i & -i) ++s ;
        printf("%d ", s);
    }
    return 0;
}
```

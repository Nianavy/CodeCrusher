https://www.hackerrank.com/challenges/c-tutorial-functions

```txt
just compare.
```

```c++
#include <iostream>
#include <cstdio>
using namespace std;

/*
Add `int max_of_four(int a, int b, int c, int d)` here.
*/
int my_max(int x, int y) {
    return (x > y ? x : y);
}

int max_of_four(int a, int b, int c, int d) {
    return my_max(my_max(a, b), my_max(c, d));
}

int main() {
    int a, b, c, d;
    scanf("%d %d %d %d", &a, &b, &c, &d);
    int ans = max_of_four(a, b, c, d);
    printf("%d", ans);
    
    return 0;
}
```

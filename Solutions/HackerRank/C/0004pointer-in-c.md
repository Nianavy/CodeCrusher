https://www.hackerrank.com/challenges/pointer-in-c

```txt
just use pointer.
```

```c
#include <stdio.h>

int my_abs(int x) {
    return (x < 0) ? -x : x;
}

void update(int *a,int *b) {
    // Complete this function   
    int ra = *a, rb = *b; 
    *a = *a + *b;
    *b = my_abs(ra - rb);
}

int main() {
    int a, b;
    int *pa = &a, *pb = &b;
    
    scanf("%d %d", &a, &b);
    update(pa, pb);
    printf("%d\n%d", a, b);

    return 0;
}
```

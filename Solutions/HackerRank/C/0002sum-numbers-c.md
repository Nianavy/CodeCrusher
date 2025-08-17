https://www.hackerrank.com/challenges/sum-numbers-c

```txt
just do it(get stdin and put stdout).
```

```c
#include <stdio.h>
#include <string.h>
#include <math.h>
#include <stdlib.h>

int main()
{
	int a, b;
    float x, y;
    
    scanf("%d %d %f %f", &a, &b, &x, &y);
    printf("%d %d\n%.1f %.1f\n", a + b, a - b, x + y, x - y);
    
    return 0;
}
```

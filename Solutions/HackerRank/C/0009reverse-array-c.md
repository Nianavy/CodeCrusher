https://www.hackerrank.com/challenges/reverse-array-c

```txt
using two pointers to iterate from both ends of the array towards the middle,
swapping elements in place to reverse the array.
```

```c
#include <stdio.h>
#include <stdlib.h>

int main()
{
    int num, *arr, i;
    scanf("%d", &num);
    arr = (int*) malloc(num * sizeof(int));
    for(i = 0; i < num; i++) {
        scanf("%d", arr + i);
    }


    /* Write the logic to reverse the array. */
    for (int i= 0; i < num / 2; ++i) {
        int t = arr[i];
        arr[i] = arr[num - 1 - i];
        arr[num - 1 - i] = t;
    }
    
    
    for(i = 0; i < num; i++)
        printf("%d ", *(arr + i));
    return 0;
}
```

https://www.hackerrank.com/challenges/frequency-of-digits-1

```txt
reads an alphanumeric string,
then iterates through its characters
to count the frequencies of digits 0-9
by mapping character ASCII values to array indices,
finally printing these counts.
```

```c
#include <stdio.h>
#include <string.h>
#include <math.h>
#include <stdlib.h>

int main() {

    /* Enter your code here. Read input from STDIN. Print output to STDOUT */    
    char s[1001];
    scanf("%s", s);
    int frequencies[10] = {0};
    for (int i = 0; s[i] != '\0'; ++i) {
        char current_char = s[i];
        if (current_char >= '0' && current_char <= '9')
            frequencies[current_char - '0']++;
    }
    for (int i = 0; i < 10; ++i) {
        printf("%d", frequencies[i]);
        if (i < 9) printf(" ");
    }
    printf("\n");
    return 0;
}
```

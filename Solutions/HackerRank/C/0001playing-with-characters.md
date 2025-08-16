https://www.hackerrank.com/challenges/playing-with-characters

```txt
Use large buffer + scanf(" %[^\n]") to skip newline and read full line.
```

```C
#include <stdio.h>
#include <string.h>
#include <math.h>
#include <stdlib.h>

#define MAX 1000

int main() 
{

    /* Enter your code here. Read input from STDIN. Print output to STDOUT */ 
    char ch;
    scanf("%c", &ch);
    printf("%c\n", ch);
    
    char str[MAX] = {'\0'};
    scanf("%s", str);
    printf("%s\n", str);
    
    char statement[MAX] = {'\0'};
    scanf(" %[^\n]", statement);
    printf("%s\n", statement);
       
    return 0;
}
```

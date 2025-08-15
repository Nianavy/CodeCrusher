https://www.acwing.com/problem/content/3719/

### Think
```
Iterate through the string,
prefix uppercase letters with _ and convert to lowercase;
output other characters unchanged,
effectively converting uppercase style to snake_case style.
```

### Ways
```C++
#include <stdio.h>
#include <ctype.h>
#include <string.h>

#define MAX 1024

int main() {
  char name[MAX];
  while (scanf("%1023s", name) == 1) {
    size_t len = strlen(name);
    for (size_t i = 0; i < len; ++i) {
      char c = name[i];
      if (c < 'a') {
        putchar('_');
        putchar(tolower((unsigned char)c));
      }
      else putchar(c);
    }
    puts("");
  }
  return 0;
}
```

https://www.acwing.com/problem/content/773/

```c++
#include <cstdio>
#include <cstring>

char str[201];

int main() {
  int T;
  scanf("%d", &T);
  getchar();

  while (T--) {
    scanf("%200[^\n]%*c", str);
    int len = strlen(str);

    if (len == 0) {
        printf(" \n");
        continue;
    }

    int cnt = 0;
    char c = str[0];

    for (int i = 0; i < len; ++i) {
      int j = i;
      while (j < len && str[j] == str[i]) {
        ++j;
      }
      if (j - i > cnt) {
        cnt = j - i;
        c = str[i];
      }
      i = j - 1; 
    }
    printf("%c %d\n", c, cnt);
  }
  return 0;
}
```

https://www.acwing.com/problem/content/775/

```c++
#include <cstdio>
#include <cstring>

char str[11], substr[4];

int main() {
  while (scanf("%s %s", str, substr) == 2) {
    int p = 0;
    int str_len = strlen(str);
    for (int i = 1; i < str_len; ++i)
      if (str[i] > str[p]) p = i;
    size_t substr_len = strlen(substr);
    char result_buf[str_len + substr_len + 1];
    strncpy(result_buf, str, p + 1);
    result_buf[p + 1] = '\0';
    strcat(result_buf, substr);
    strcat(result_buf, str + p + 1);
    printf("%s\n", result_buf);
  }
  return 0;
}
```

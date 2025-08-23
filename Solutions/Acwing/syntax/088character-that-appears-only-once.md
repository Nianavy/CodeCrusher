https://www.acwing.com/problem/content/774/

```c++
#include <cstdio>
#include <cstring>
#define MAX_LEN 100001

int main() {
  char s[MAX_LEN];
  scanf("%s", s);
  s[strcspn(s, "\n")] = 0;
  int freq[26] = {0};
  int len = strlen(s);
  for (int i = 0; i < len; ++i) {
    if (s[i] >= 'a' && s[i] <= 'z') freq[s[i] - 'a']++;
  }
  char result = ' ';
  bool found = false;
  for (int i = 0; i < len; ++i) {
    if (freq[s[i] - 'a'] == 1) {
      result = s[i];
      found = true;
      break;
    }
  }
  if (found) printf("%c\n", result);
  else printf("no\n");
  return 0;
}
```

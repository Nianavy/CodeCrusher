https://www.acwing.com/problem/content/764/

```c++
#include <cstdio>
#include <cstring>

int main() {
  char a[101], b[101];
  double k;
  memset(a, 0, sizeof a);
  memset(b, 0, sizeof b);
  scanf("%lf %s %s", &k, a, b);
  int len = strlen(a), n = 0;
  for (int i = 0; i < len; ++i) {
    if (a[i] == b[i]) ++n;
  }
  if((double)n / len >= k) puts("yes");
  else puts("no");
  return 0;
}
```

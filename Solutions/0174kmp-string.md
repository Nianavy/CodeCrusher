https://www.acwing.com/problem/content/833/

### Think
```
KMP
```

### Ways
By C++
```C++
#include <cstdio>
const int N = 1e5 + 10, M = 1e6 + 10;
int n, m, ne[N];
char s[M], p[N];

int main() {
  scanf("%d %s %d %s", &n, p + 1, &m, s + 1);
  
  for (int i = 2, j = 0; i <= n; ++i) {
    while (j && p[i] != p[j + 1]) j = ne[j];
    if (p[i] == p[j + 1]) ++j;
    ne[i] = j;
  }
  
  for (int i = 1, j = 0; i <= m; ++i) {
    while (j && s[i] != p[j + 1]) j = ne[j];
    if (s[i] == p[j + 1]) ++j;
    if (j == n) {
      printf("%d ", i - n);
      j = ne[j];
    }
  }
  return 0;
}
```

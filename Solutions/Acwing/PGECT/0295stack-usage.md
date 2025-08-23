https://www.acwing.com/problem/content/3594/

```txt
simulate push, pop,
and peek operations using a stack,
process each group of commands until input 0 ends.
```

```c++
#include <cstdio>
#include <cstring>

const int N = 10010;
int n, stk[N], top;

int main() {
  while (scanf("%d", &n) == 1, n) {
    top = 0;
    while (n--) {
      char op[2];
      scanf("%s", op);
      if (*op == 'P') {
        int x;
        scanf("%d", &x);
        stk[++top] = x;
      }
      else if (*op == 'O') {
        if (top) --top;
      }
      else {
        if (top) printf("%d\n", stk[top]);
        else puts("E");
      }
    }
    puts("");
  }
  
  return 0;
}
```

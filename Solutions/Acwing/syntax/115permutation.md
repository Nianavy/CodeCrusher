https://www.acwing.com/problem/content/825/

```c++
#include <cstdio>

const int N = 9;
int n, path[N];
bool used[N];

void perm(int u) {
  if (u == n) {
    for (int i = 0; i < n; ++i) printf("%d ", path[i] + 1);
    printf("\n");
    return;
  }
  for (int i = 0; i < n; ++i) {
    if (!used[i]) {
      used[i] = true;
      path[u] = i;
      perm(u + 1);
      used[i] = false;
    }
  }
}

int main() {
  scanf("%d", &n);
  perm(0);
  return 0;
}
```

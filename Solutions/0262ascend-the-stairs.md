https://www.acwing.com/problem/content/3646/

### Think
```
recurrence formula

or

DFS
```

### Ways
```C++
// recurrence formula
#include <cstdio>

const int N = 25;
int n, f[N];

int main() {
  scanf("%d", &n);
  f[0] = 1;
  
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= 3; ++j)
      if (i - j >= 0) f[i] += f[i - j];
  
  printf("%d\n", f[n]);
  return 0;
}


// DFS
#include <cstdio>

const int N = 25;
int n, f[N];

int dfs(int);

int main() {
  scanf("%d", &n);
  
  printf("%d\n", dfs(0));
  
  return 0;
}

int dfs(int x) {
  if (x == n) return 1;
  if (x > n) return 0;
  return dfs(x + 1) + dfs(x + 2) + dfs(x + 3);
}
```

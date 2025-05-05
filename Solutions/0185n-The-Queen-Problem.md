https://www.acwing.com/problem/content/845/

### Think
```
DFS
```

### Ways
By C++
#### cell by cell
```C++
#include <cstdio>

const int N = 10;
bool row[N], col[N], dg[N * 2], udg[N * 2];
char g[N][N];
int n;

void dfs(int x, int y, int s) {
  if (s > n) return;
  if (y == n) y = 0, ++x;
  if (x == n) {
    if (s == n) {
      for (int i = 0; i < n; ++i) puts(g[i]);
      puts("");
    }
    return;
  }
  g[x][y] = '.';
  dfs(x, y + 1, s);
  if (!row[x] && !col[y] && !dg[x + y] && !udg[x - y + n]) {
    row[x] = col[y] = dg[x + y] = udg[x - y + n] = true;
    g[x][y] = 'Q';
    dfs(x, y + 1, s + 1);
    g[x][y] = '.';
    row[x] = col[y] = dg[x + y] = udg[x - y + n] = false;
  }
}

int main() {
  scanf("%d", &n);
  dfs(0, 0, 0);
  return 0;
}
```

#### row by row
```C++
 #include <cstdio>
 
 const int N = 20;
 bool col[N], dg[N * 2], udg[N * 2];
 char g[N][N];
 int n;
 
 void dfs(int u) {
   if (u == n) {
     for (int i = 0; i < n; ++i) puts(g[i]);
     puts("");
     return;
   }
   
   for (int i = 0; i < n; ++i) {
     if (!col[i] && !dg[u + i] && !udg[n - u + i]) {
       g[u][i] = 'Q';
       col[i] = dg[u + i] = udg[n - u + i] = true;
       dfs(u + 1);
       col[i] = dg[u + i] = udg[n - u + i] = false;
       g[u][i] = '.';
      }
   }
 }
 
 int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i)
   for (int j = 0; j < n; ++j)
     g[i][j] = '.';
  dfs(0);
  return 0;
 }
```

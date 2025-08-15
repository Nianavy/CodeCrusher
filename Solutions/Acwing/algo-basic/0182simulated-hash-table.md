https://www.acwing.com/problem/content/842/

### Think
```
Hash-Table
```
### Ways
By C++
#### Open addressing
```C++
#include <cstdio>
#include <cstring>

const int N = 2e5 + 3, null = 0x3f3f3f3f;
int h[N];

int find(int x) {
  int t = (x % N + N) % N;
  while (h[t] != null && h[t] != x) {
    ++t;
    if (t == N) t = 0;
  }
  return t;
}

int main() {
  memset(h, 0x3f, sizeof h);
  int n;
  scanf("%d", &n);
  while (n--) {
    char op[2];
    int x;
    scanf("%s%d", op, &x);
    if (*op == 'I') h[find(x)] = x;
    else {
      if (h[find(x)] == null) puts("No");
      else puts("Yes");
    }
  }
  return 0;
}
```
#### Zipper
```C++
#include <cstdio>
#include <cstring>

const int N = 1e5 + 3;
int h[N], e[N], ne[N], idx;

void insert(int x) {
  int k = (x % N + N) % N;
  e[idx] = x;
  ne[idx] = h[k];
  h[k] = idx++;
}

bool find(int x ) {
  int k = (x % N + N) % N;
  for (int i = h[k]; i != -1; i = ne[i])
    if (e[i] == x) return true;
  return false;
}

int main() {
  int n;
  scanf("%d", &n);
  memset(h, -1, sizeof h);
  while (n--) {
    char op[2];
    int x;
    scanf("%s%d", op, &x);
    if (*op == 'I') insert(x);
    else {
      if (find(x)) puts("Yes");
      else puts("No");
    }
  }
  return 0;
}
```

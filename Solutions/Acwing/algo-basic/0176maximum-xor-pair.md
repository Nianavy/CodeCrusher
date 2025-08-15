https://www.acwing.com/problem/content/145/

### Think
```
Trie-tree
```

### Ways
By C++
```C++
#include <cstdio>
#include <algorithm>
using namespace std;

const int N = 1e5 + 10, M = 3e6 + 1e5 + 10;
int n, a[N], son[M][2], idx;

void insert(int x) {
  int p = 0;
  for (int i = 30; i >= 0; --i) {
    int &s = son[p][x >> i & 1];
    if (!s) s = ++idx;
    p = s;
  }
}

int search(int x) {
  int p = 0, res = 0;
  for (int i = 30; i >= 0; --i) {
    int s= x >> i & 1;
    if (son[p][!s]) {
      res += 1 << i;
      p = son[p][!s];
    }
    else p = son[p][s];
  }
  return res;
}

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) {
    scanf("%d", &a[i]);
    insert(a[i]);
  }
  int res = 0;
  for (int i = 0; i < n; ++i) res = max(res, search(a[i]));
  printf("%d\n", res);
  
  return 0;
}
```

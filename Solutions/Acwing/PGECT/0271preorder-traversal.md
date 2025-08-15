https://www.acwing.com/problem/content/5073/

### Think
```
Given the inorder sequence b and postorder sequence a of a binary tree,
recursively identify the root as the last element in postorder,
use a hash map to find its position in inorder,
partition the tree accordingly,
and output the root node value.
```

### Ways
```C++
#include <cstdio>
#include <unordered_map>

const int N = 50010;
int n, a[N], b[N], ans;
std::unordered_map<int, int> p;

void build(int al, int ar, int bl, int br) {
  if (al > ar) return;
  
  int root = a[ar];
  int k = p[root];
  ans = root;
  
  build(al, k - 1 - bl + al, bl, k - 1);
  build(k - bl + al, ar - 1, k + 1, br);
}

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  for (int i = 0; i < n; ++i) {
    scanf("%d", &b[i]);
    p[b[i]] = i;
  }
  
  build(0, n - 1, 0, n - 1);
  printf("%d\n", ans);
  
  return 0;
}
```

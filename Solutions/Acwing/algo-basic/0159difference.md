https://www.acwing.com/problem/content/description/799/

### Think
```
difference
```

### Ways
By C++
```C++
#include <cstdio>
#include <vector>

using namespace std;

void insert(vector<int>& b, int l, int r, int c) {
  b[l] += c;
  if (r + 1 < b.size()) {
    b[r + 1] -= c;
  }
}

vector<int> restoreArray(const vector<int>& b) {
  int n = b.size();
  vector<int> a(n);
  a[0] = b[0];
  for (int i = 1; i < n; ++i) {
    a[i] = a[i - 1] + b[i];
  }
  return a;
}

int main() {
  int n, m;
  scanf("%d %d", &n, &m);

  vector<int> a(n + 1, 0);
  vector<int> b(n + 1, 0);

  for (int i = 1; i <= n; ++i) scanf("%d", &a[i]);

  for (int i = 1; i <= n; ++i) insert(b, i, i, a[i]);

  while (m--) {
    int l, r, c;
    scanf("%d%d%d", &l, &r, &c);
    insert(b, l, r, c);
  }

  vector<int> result = restoreArray(b);

  for (int i = 1; i <= n; ++i) printf("%d ", result[i]);
  printf("\n");

  return 0;
}
```

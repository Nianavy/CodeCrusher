https://www.acwing.com/problem/content/3530/

### Think
```
Simulate up to four 90° clockwise rotations to check matrix match.
```

### Ways
```C++
#include <iostream>
#include <algorithm>
#include <vector>

typedef std::vector<std::vector<int>> VVI;
int n;
VVI a, b;

void rotate(VVI &c) {
  for (int i = 0; i < n; ++i)
    for (int j = 0; j < i; ++j)
      std::swap(c[i][j], c[j][i]);
      
  for (int j = 0; j < n; ++j)
    for (int i = 0, k = n - 1; i < k; ++i, --k)
      std::swap(c[i][j], c[k][j]);
}

int main() {
  std::cin >> n;
  a = b = VVI(n, std::vector<int>(n));
  for (int i = 0; i < n; ++i)
    for (int j = 0; j < n; ++j)
      std::cin >> a[i][j];
  
  for (int i = 0; i < n; ++i)
    for (int j = 0; j < n; ++j)
      std::cin >> b[i][j];
  
  for (int i = 0; i < 4; ++i) {
    if (a == b) {
      std::cout << i * 90 << std::endl;
      return 0;
    }
    rotate(b);
  }
  std::cout << -1 << std::endl;
  return 0;
}
```

https://www.acwing.com/problem/content/665/

```c++
#include <cstdio>
#include <algorithm>
#include <vector>

int main() {
  std::vector<int> array;
  int x, y, z;
  scanf("%d %d %d", &x, &y, &z);
  array.push_back(x);
  array.push_back(y);
  array.push_back(z);
  std::sort(array.begin(), array.end());
  for (const auto &it : array) printf("%d\n", it);
  puts("");
  printf("%d\n%d\n%d\n", x, y, z);
  return 0;
}
```

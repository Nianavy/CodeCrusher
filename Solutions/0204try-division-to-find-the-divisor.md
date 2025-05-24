https://www.acwing.com/problem/content/871/

### Think
```
Try division to find the divisor
```

### Ways
By C++
```C++
#include <cstdio>
#include <vector>
#include <algorithm>

std::vector<int> get_divisors(int x) {
  std::vector<int> res;
  for (int i = 1; i <= x / i; ++i) {
    if (x % i == 0) {
      res.push_back(i);
      if (i != x / i) res.push_back(x / i);
    }
  }
  std::sort(res.begin(), res.end());
  return res;
}

int main() {
  int n;
  scanf("%d", &n);
  while (n--) {
    int x;
    scanf("%d", &x);
    auto res = get_divisors(x);
    for (auto x : res) printf("%d ", x);
    puts("");
  }
  return 0;
}
```

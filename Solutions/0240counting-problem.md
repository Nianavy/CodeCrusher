https://www.acwing.com/problem/content/340/

### Think
```
Dynamic Programming
```

### Ways
```C++
#include <vector>
#include <cstdio>
#include <algorithm>

const int N = 10;

int get(std::vector<int> num, int l, int r) {
  int res = 0;
  for (int i = l; i >= r; --i) res = res * 10 + num[i];
  return res;
}

int power10(int x) {
  int res = 1;
  while (x--) res *= 10;
  return res;
}

int count(int n, int x) {
  if (!n) return 0;
  std::vector<int> num;
  while (n) {
    num.push_back(n % 10);
    n /= 10;
  }
  n = num.size();
  int res = 0;
  for (int i = n - 1 - !x; i >= 0; --i) {
    if (i < n - 1) {
      res += get(num, n - 1, i + 1) * power10(i);
      if (!x) res -= power10(i);
    }
    if (num[i] == x) res += get(num, i - 1, 0) + 1;
    else if (num[i] > x) res += power10(i);
  }
  return res;
}

int main() {
  int a, b;
  while (scanf("%d%d", &a, &b), a) {
    if (a > b) std::swap(a, b);
    for (int i = 0; i <= 9; ++i) printf("%d ", count(b, i) - count(a - 1, i));
    puts("");
  }
  return 0;
}
```

https://www.acwing.com/problem/content/797/

### Think
```
Prefix-Sum
```

### Ways
By C++
```C++
#include <cstdio>
#include <vector>
using namespace std;

vector<int> calcPreSum(const vector<int> &a) {
  int n = a.size();
  vector<int> presum(n + 1, 0);
  for (int i = 1; i <= n; ++i)
    presum[i] = presum[i - 1] + a[i - 1];
  return presum;
}

int main() {
  int n, m;
  scanf("%d %d", &n, &m);
  
  vector<int> a(n);
  for (int i = 0; i < n; ++i) scanf("%d", &a[i]);
  
  vector<int> s = calcPreSum(a);
  
  while (m--) {
    int l, r;
    scanf("%d %d", &l, &r);
    printf("%d\n", s[r] - s[l - 1]);
  }
  
  return 0;
}
```

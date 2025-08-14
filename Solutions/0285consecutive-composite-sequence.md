https://www.acwing.com/problem/content/3532/

### Think
```
Use linear sieve to mark composites,
then scan for the longest consecutive composite sequence.
```

### Ways
```C++
#include <iostream>
#include <algorithm>
#include <cstring>

const int N = 10010;
int primes[N], cnt;
bool st[N];

void get_primes(int n) {
  for (int i = 2; i <= n; ++i) {
    if (!st[i]) primes[cnt++] = i;
    for (int j = 0; primes[j] * i <= n; ++j) {
      st[primes[j] * i] = true;
      if (i % primes[j] == 0) break;
    }
  }
}

int main() {
  int a, b;
  std::cin >> a >> b;
  
  get_primes(b);
  
  int len = 0, end;
  for (int i = a, s = 0; i <= b; ++i)
    if (!st[i]) s = 0;
    else {
      ++s;
      if (s > len) len = s, end = i;
    }
    
  for (int i = end - len + 1; i <= end; ++i) std::cout << i << ' ';
    
  return 0;
}
```

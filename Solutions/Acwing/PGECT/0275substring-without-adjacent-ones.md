https://www.acwing.com/problem/content/3709/

### Think
```
Problem: Count the number of binary sequences of length n that do not contain consecutive 1s.
Approach: Brute-force enumeration of all $2^n$ possible binary sequences.
Validation: For each sequence, check for consecutive 1s by iterating through adjacent bits using bitwise operations. If found, the sequence is invalid.
Counting: Only valid sequences are counted towards the final result.
```

### Ways
```C++
#include <cstdio>
#include <algorithm>

int main() {
  int n;
  scanf("%d", &n);
  
  int res = 0;
  for (int i = 0; i < 1 << n; ++i) {
    int t = 1;
    for (int j = 1; j < n; ++j)
      if ((i >> j - 1 & 1) && (i >> j & 1)) {
        t = 0;
        break;
      }
    res += t;
  }
  printf("%d\n", res);
  return 0;
}
```

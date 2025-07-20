https://www.acwing.com/problem/content/3705/

### Think
```
Reverse processing of hexadecimal strings
Digit-wise addition without carry
Reverse output of results
```

### Ways
```C++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>

int get_int(char);
char get_char(int);

int main() {
  std::string a, b;
  while (std::cin >> a >> b) {
    std::vector<int> A, B;
    for (int i = a.size() - 1; i >= 0; --i) A.push_back(get_int(a[i]));
    for (int i = b.size() - 1; i >= 0; --i) B.push_back(get_int(b[i]));
    std::vector<int> C;
    for (int i = 0; i < A.size() || i < B.size(); ++i) {
      int t = 0;
      if (i < A.size()) t += A[i];
      if (i < B.size()) t += B[i];
      C.push_back(t % 16);
    }
    for (int i = C.size() - 1; i >= 0; --i) std::cout<< get_char(C[i]);
    std::cout << std::endl;
  }
  return 0;
}

int get_int(char c) {
  if (c <= '9') return c - '0';
  return c - 'A' + 10;
}

char get_char(int x) {
  if (x < 10) return x + '0';
  return x - 10 + 'A';
}
```

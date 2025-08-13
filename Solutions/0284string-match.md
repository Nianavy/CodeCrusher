https://www.acwing.com/problem/content/3531/

### Think
```
Simple pattern matching with [...] character sets
and case insensitivity to filter and output matching strings.
```

### Ways
```C++
#include <iostream>
#include <algorithm>

const int N = 1010;
int n;
std::string strs[N], p;

std::string filter(std::string str) {
  std::string res;
  for (auto c: str) res += tolower(c);
  return res;
}

bool match(std::string a, std::string p) {
  for (int i = 0, j = 0; i < a.size() || j < p.size(); ++i) {
    if (i == a.size() || j == p.size()) return false;
    if (p[j] != '[') {
      if (a[i] != p[j]) return false;
      ++j;
    }
    else {
      std::string s;
      ++j;
      while (p[j] != ']') s += p[j++];
      ++j;
      if (s.find(a[i]) == -1) return false;
    }
  }
  return true;
}

int main() {
  std::cin >> n;
  for (int i = 0; i < n; ++i) std::cin >> strs[i];
  std::cin >> p;
  p = filter(p);
  
  for (int i = 0; i < n; ++i)
    if (match(filter(strs[i]), p)) 
      std::cout << i + 1 << ' ' << strs[i] << std::endl;
  
  return 0;
}
```

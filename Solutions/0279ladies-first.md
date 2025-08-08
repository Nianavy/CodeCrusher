https://www.acwing.com/problem/content/3703/

### Think
```
After removing leading 'F',
greedily compute max pairs using t = max(t+1, cnt).
```

### Ways
```C++
#include <iostream>
#include <algorithm>

int main() {
  std::string str;
  std::cin >> str;
  
  int k = 0;
  while (k < str.size() && str[k] == 'F') ++k;
  str = str.substr(k);
  
  int t = 0, cnt = 0;
  for (auto c: str)
    if (c == 'M') ++cnt;
    else t = std::max(t + 1, cnt);
  
  std::cout << t << std::endl;
  
  return 0;
}
```

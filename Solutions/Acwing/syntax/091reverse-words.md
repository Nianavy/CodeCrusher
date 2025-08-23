https://www.acwing.com/problem/content/777/

```c++
#include <iostream>

int main() {
  std::string words[100];
  int k = 0;
  while (std::cin >> words[k]) ++k;
  while (k--) std::cout << words[k] << ' ';
  return 0;
}
```

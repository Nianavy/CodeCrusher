https://www.acwing.com/problem/content/767/

```c++
#include <iostream>

int main() {
  std::string line;
  getline(std::cin, line);
  for (char c: line) std::cout << c << ' ';
  std::cout << '\n';
  return 0;
}
```

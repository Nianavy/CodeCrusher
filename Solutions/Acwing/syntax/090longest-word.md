https://www.acwing.com/problem/content/776/

```c++
#include <iostream>

int main() {
  std::string res, word;
  while (std::cin >> word) {
    if (word.back() == '.') word.pop_back();
    if (word.size() > res.size()) res = word;
  }
  std::cout << res << std::endl;
  return 0;
}
```

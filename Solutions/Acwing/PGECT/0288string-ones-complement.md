https://www.acwing.com/problem/content/3587/

```txt
Maps each alphabetic character in the input string to its reverse in the alphabet (a↔z, b↔y, ...),
leaves non-letters unchanged, and stops when "!" is entered.
```

```C++
#include <iostream>
#include <cstring>
#include <algorithm>

int main() {
  std::string str;
  
  while (std::cin >> str, str != "!") {
    for (auto &c: str)
      if (c >= 'a' && c <= 'z') c = 25 - (c - 'a') + 'a';
      else if (c >= 'A' && c <= 'Z') c = 25 - (c - 'A') + 'A';
    std::cout << str << std::endl;
  }
  
  return 0;
}
```

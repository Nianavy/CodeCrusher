https://www.acwing.com/problem/content/3724/

```txt
sort the string in descending order;
if the resulting number is divisible by 30,
print it, otherwise print -1.
```

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

int main() {
  std::string str;
  std::cin >> str;
  std::sort(str.begin(), str.end(), std::greater<>());
  if (stoi(str) % 30 == 0) std::cout << str << std::endl;
  else std::cout << -1 << std::endl;
  return 0;
}
```

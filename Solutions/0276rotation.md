https://www.acwing.com/problem/content/3710/

### Think
```
If string a is a rotation of b,
then a must be a substring of b + b
transforming a rotational problem into
a substring search via space-for-time tradeoff.
```

### Ways
```C++
#include <iostream>

int main() {
  std::string a, b;
  while (std::cin >> a >> b) {
    std::string c = b + b;
    if (a.size() <= b.size() && c.find(a) != -1)
      std::cout << "yes\n";
    else std::cout << "no\n";
  }
  return 0;
}
```

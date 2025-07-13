https://www.acwing.com/problem/content/3695/

### Think
```
enumerate + brute force
LCS
```

### Ways
```C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

int main() {
  string a, b;
  cin >> a >> b;
  
  string res;
  for (int len = min(a.size(), b.size()); len; --len) {
    for (int i = a.size() - len; i >= 0; --i) {
      for (int j = b.size() -len; j >= 0; --j) {
        string s1 = a.substr(i, len), s2 = b.substr(j, len);
        if (s1 == s2) {
          res = s1;
          break;
        }
      }
      if (res.size()) break;
    }
    if (res.size()) break;
  }
  cout << res.size() << endl;
  cout << res << endl;
  return 0;
}
```

https://www.acwing.com/problem/content/830/

### Think
```
Array simulate everything.
```
### Ways
By C++
```C++
#include <iostream>
using namespace std;

const int N = 1e5 + 10;
int m, stk[N], t;

int main() {
  cin >> m;
  while (m--) {
    string op;
    int x;
    cin >> op;
    if (op == "push") {
      cin >> x;
      stk[++t] = x;
    }
    else if (op == "pop") t--;
    else if (op == "empty") cout << (t ? "NO" : "YES") << endl;
    else cout << stk[t] << endl;
  }
  return 0;
}
```

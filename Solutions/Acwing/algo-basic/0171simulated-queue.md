https://www.acwing.com/problem/content/831/

### Think
```
queue
```

### Ways
By C++
```C++
#include <iostream>
using namespace std;
const int N = 1e5 + 10;
int m, q[N], h, t = -1;
int main() {
  cin >> m;
  while (m--) {
    string op;
    int x;
    cin >> op;
    if (op == "push") {
      cin >> x;
      q[++t] = x;
    }
    else if (op == "pop") h++;
    else if (op == "empty") cout << (h <= t ? "NO" : "YES") << endl;
    else cout << q[h] << endl;
  }
  return 0;
}
```

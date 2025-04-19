https://www.acwing.com/problem/content/829/

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
int m, e[N], l[N], r[N], idx;

void init() {
  r[0] = 1, l[1] = 0, idx = 2;  
}

void insert(int k, int x) {
  e[idx] = x;
  l[idx] = k, r[idx] = r[k];
  l[r[k]] = idx, r[k] = idx++;
}

void remove(int k) {
  l[r[k]] = l[k];
  r[l[k]] = r[k];
}

int main() {
  init();
  cin >> m;
  while (m--) {
    string op;
    cin >> op;
    int k, x;
    if (op == "L") {
      cin >> x;
      insert(0, x);
    }
    else if (op == "R") {
      cin >> x;
      insert(l[1], x);
    }
    else if (op == "D") {
      cin >> k;
      remove(k + 1);
    }
    else if (op == "IL") {
      cin >> k >> x;
      insert(l[k + 1], x);
    }
    else {
      cin >> k >> x;
      insert(k + 1, x);
    }
  }
  for (int i = r[0]; i != 1; i = r[i]) cout << e[i] << ' ';
  cout << endl;
  return 0;
}
```

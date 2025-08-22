https://www.acwing.com/problem/content/770/

```c++
#include <iostream>
using namespace std;
int main()
{
    string a, b;
    getline(cin, a);
    getline(cin, b);

    for (auto &c : a) c = tolower(c);
    for (auto &c : b) c = tolower(c);

    if (a == b) cout << '=' << endl;
    else if (a > b) cout << '>' << endl;
    else cout << '<' << endl;

    return 0;
}
```

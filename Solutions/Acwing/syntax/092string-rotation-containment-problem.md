https://www.acwing.com/problem/content/778/

```c++
#include <iostream>

using namespace std;

int main()
{
    string a, b;
    cin >> a >> b;

    if (b.size() > a.size()) swap(a, b);

    int n = a.size();
    a += a;

    bool flag = false;
    for (int i = 0; i < n; i ++ )
        if (a.substr(i, b.size()) == b)
        {
            flag = true;
            break;
        }

    if (flag) cout << "true" << endl;
    else cout << "false" << endl;

    return 0;
}
```

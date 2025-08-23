https://www.acwing.com/problem/content/780/

```c++
#include <iostream>

using namespace std;

int main()
{
    string line;
    cin >> line;

    string a, b, c;
    int k = 0;
    while (line[k] != ',') a += line[k ++ ];
    k ++ ;
    while (line[k] != ',') b += line[k ++ ];
    k ++ ;
    while (k < line.size()) c += line[k ++ ];

    int l = 0;
    while (l < a.size())
    {
        if (a.substr(l, b.size()) == b) break;
        l ++ ;
    }

    int r = a.size() - 1;
    while (r >= 0)
    {
        if (a.substr(r, c.size()) == c) break;
        r -- ;
    }

    if (l + b.size() <= r) cout << r - l - b.size() << endl;
    else cout << -1 << endl;

    return 0;
}
```

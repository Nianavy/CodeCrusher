https://www.acwing.com/problem/content/772/

```c++
#include <iostream>

using namespace std;

int main()
{
    string line;
    string a, b;
    getline(cin, line);
    cin >> a >> b;

    for (int i = 0; i < line.size(); i ++ )
    {
        int j = i;
        string word;
        while (j < line.size() && line[j] != ' ') word += line[j ++ ];
        i = j;

        if (word == a) cout << b << ' ';
        else cout << word << ' ' ;
    }

    return 0;
}
```

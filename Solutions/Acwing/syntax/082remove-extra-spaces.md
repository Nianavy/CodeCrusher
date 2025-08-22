https://www.acwing.com/problem/content/768/

```c++
#include <iostream>
using namespace std;
int main()
{
    string line;
    getline(cin, line);

    for (int i = 0; i < line.size(); i ++ )
        if (line[i] != ' ') cout << line[i];
        else
        {
            int j = i;
            while (j < line.size() && line[j] == ' ') j ++ ;
            cout << ' ';
            i = j - 1;
        }

    return 0;
}
```

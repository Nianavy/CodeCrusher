https://www.acwing.com/problem/content/766/

```c++
#include <iostream>
using namespace std;
int main()
{
    string a;
    getline(cin, a);

    string b;
    for (int i = 0; i + 1 < a.size(); i ++ ) 
        b += (char)(a[i] + a[i + 1]);
    b += (char)(a[0] + a.back());

    cout << b << endl;
    return 0;
}
```

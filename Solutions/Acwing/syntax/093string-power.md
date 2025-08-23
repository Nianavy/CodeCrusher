https://www.acwing.com/problem/content/779/

```c++
#include <iostream>

using namespace std;

int main()
{
    string str;
    while (cin >> str, str != ".")
    {
        int k = 1;
        while (k <= str.size())
        {
            if (str.size() % k == 0)
            {
                bool flag = true;
                for (int i = k; i < str.size(); i += k)
                    for (int j = 0; j < k; j ++ )
                        if (str[j] != str[i + j])
                            flag = false;
                if (flag) break;
            }
            k ++ ;
        }

        cout << str.size() / k << endl;
    }

    return 0;
} 
```

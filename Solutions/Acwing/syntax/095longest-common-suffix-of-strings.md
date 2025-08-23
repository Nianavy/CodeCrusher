https://www.acwing.com/problem/content/781/

```c++
#include <iostream>
using namespace std;
int main(){
    string words[200];
    int n;
    while (cin >> n, n){
        for (int i = 0; i < n; i ++ ) cin >> words[i];
        int k = 1;
        while (true){
            bool flag = true;
            for (int i = 0; i < n; i ++ ){
                if (words[i].size() < k){
                    flag = false;
                    break;
                }
                else if (words[i].substr(words[i].size() - k, k) != words[0].substr(words[0].size() - k, k)){
                    flag = false;
                    break;
                }
            }
            if (!flag) break;
            k ++ ;
        }
        k -- ;
        cout << words[0].substr(words[0].size() - k, k) << endl;
    }
    return 0;
}
```

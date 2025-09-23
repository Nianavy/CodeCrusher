https://www.nowcoder.com/exam/oj?page=1&tab=%E7%AE%97%E6%B3%95%E5%AD%A6%E4%B9%A0%E7%AF%87&topicId=383

```c++
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double a, b;
    while (cin >> a >> b) {
        cout << fixed << setprecision(3)
             << b / a * 100 << '%'
        << endl;
    }
}
```

https://www.hackerrank.com/challenges/c-tutorial-basic-data-types

```txt
just using in/out.
```

```c++
#include <cstdio>
#include <iostream>
#include <string>
using namespace std;

int main() {
    // way 1
    /*
    int i;
    long li;
    char c;
    string f_str, d_str;
    cin >> i >> li >> c >> f_str >> d_str;

    cout << i << "\n"
         << li << "\n"
         << c << "\n"
         << f_str << "\n"
         << d_str << endl;
    */
    
    // way 2
    int i;
    long li;
    char c;
    float f;
    double d;
    scanf("%d %ld %c %f %lf", &i, &li, &c, &f, &d);
    printf("%d\n%ld\n%c\n%.3f\n%.9lf\n", i, li, c, f, d);
    return 0;
}
```

https://www.hackerrank.com/challenges/arrays-introduction

```txt
Use STL'Vector.
```

```c++
#include <cmath>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;


int main() {
    /* Enter your code here. Read input from STDIN. Print output to STDOUT */   
    int n;
    scanf("%d", &n);
    vector<int> arr;
    for (int i = 0; i < n; ++i) {
        int val = 0;
        scanf("%d", &val);
        arr.push_back(val);
    }
    for (auto i = arr.rbegin(); i != arr.rend(); ++i) printf("%d ", *i);
    return 0;
}
```

https://www.hackerrank.com/challenges/variable-sized-arrays

```txt
using nested std::vector structures
to store multiple variable-length integer arrays
and performing indexed queries based on zero-based indexing.
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
    int N, Q;
    cin >> N >> Q;
    
    vector<vector<int>> arrays(N);
    
    for (int i = 0; i < N; ++i) {
        int k;
        cin >> k;
        arrays[i].resize(k);
        for (int j = 0; j < k; ++j) cin >> arrays[i][j];
    }
    
    for (int q_idx = 0; q_idx < Q; ++q_idx) {
        int i, j;
        cin >> i >> j;
        cout << arrays[i][j] << endl;
    }
    return 0;
}
```

https://www.acwing.com/problem/content/5072/

### Think
```
uses a deque-like approach to
simulate popping elements from either end of array
a to form array b.
It maintains two pointers, front and back, representing the current queue ends,
and tries to sequentially match and remove elements corresponding to b.
The algorithm checks if b can be obtained entirely through this double-ended popping process.
```

### Ways
```C++
#include <cstdio>

const int N = 100010;
int n, m;
int a[N], b[N], q[N];

bool check() {
    int front = 0, back = -1;
    int j = 0;

    for (int i = 0; i < n; ++i) {
        q[++back] = a[i];

        while (front <= back) {
            if (b[j] == q[front]) {
                ++j; ++front;
            } else if (b[j] == q[back]) {
                ++j; --back;
            } else {
                break;
            }
        }
        
        if (j == n) return true;
    }

    return j == n;
}

int main() {
    scanf("%d %d", &n, &m);
    for (int i = 0; i < n; ++i) scanf("%d", &a[i]);

    while (m--) {
        for (int i = 0; i < n; ++i) scanf("%d", &b[i]);
        if (check()) puts("yes");
        else puts("no");
    }
    return 0;
}
```

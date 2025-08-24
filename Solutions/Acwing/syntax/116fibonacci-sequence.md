https://www.acwing.com/problem/content/19/

```c++
class Solution {
static const int N = 100;
int M[N];
bool IM;
public:
    Solution(): IM(false) {
      memset(M, -1, sizeof M);
      IM = true;
    }
    
    int Fibonacci(int n) {
      if (M[n] != -1) return M[n];
      if (n == 0) { M[n] = 0; return 0; }
      if (n <= 2) { M[n] = 1; return 1; }
      return M[n] = Fibonacci(n - 1) + Fibonacci(n - 2);
    }
};
```

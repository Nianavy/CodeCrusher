https://www.acwing.com/problem/content/19/

```c++
class Solution {
  
static const int N = 100;
int M[N];

public:
    Solution() { memset(M, -1, sizeof M); }
    
    int Fibonacci(int n) {
      if (M[n] != -1) return M[n];
      return M[n] = n == 0 ? 0 : n <= 2 ? 1 : Fibonacci(n - 1) + Fibonacci(n - 2);
    }
};
```

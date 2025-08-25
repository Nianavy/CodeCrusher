https://www.acwing.com/problem/content/25/

```c++
class Solution {
public:
    int NumberOf1(int n) {
      int cnt = 0;
      while (n != 0) {
        n = n & (n - 1);
        ++cnt;
      }
      return cnt;
    }
};
```

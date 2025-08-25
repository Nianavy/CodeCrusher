https://www.acwing.com/problem/content/80/

```c++
class Solution {
public:
    int getSum(int n) {
      int ans = n;
      n > 0 && (ans += getSum(n - 1));
      return ans;
    }
};
```

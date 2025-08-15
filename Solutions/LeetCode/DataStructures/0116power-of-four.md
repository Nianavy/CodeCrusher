https://leetcode.cn/problems/power-of-four

### Think
```
mod
```

### Ways
By C++
```C++
class Solution {
public:
    bool isPowerOfFour(int n) {
        return n > 0 && (n & (n - 1)) == 0 && n % 3 == 1;
    }
};
```

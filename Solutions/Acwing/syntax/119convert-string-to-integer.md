https://www.acwing.com/problem/content/83/

```c++
class Solution {
public:
    int strToInt(string str) {
      int len = str.length();
      if (len == 0) return 0;
      int i = 0;
      while (i < len && str[i] == ' ') ++i;
      int sign = 1;
      if (i < len && (str[i] == '+' || str[i] == '-')) {
        sign = (str[i] == '-') ? -1 : 1;
        ++i;
      }
      long long result = 0;
      while (i < len && isdigit(str[i])) {
        result = result * 10 + (str[i] - '0');
        if (sign * result > INT_MAX) return INT_MAX;
        else if (sign * result < INT_MIN) return INT_MIN;
        ++i;
      }
      return sign * result;
    }
};
```

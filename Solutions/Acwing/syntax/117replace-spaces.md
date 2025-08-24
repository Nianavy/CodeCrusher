https://www.acwing.com/problem/content/17/

```c++
class Solution {
public:
    string replaceSpaces(string &str) {
      string ans;
      for (const auto &c: str) {
        if (c == ' ') ans += "%20";
        else ans += c;
      }
      return ans;
    }
};
```

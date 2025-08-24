https://www.acwing.com/problem/content/74/

```c++
class Solution {
public:
    string leftRotateString(string str, int n) {
        int len = str.length();
        if (len == 0 || n <= 0 || n >= len) return str;
        string ans = str.substr(n) + str.substr(0, n);
        return ans;
    }
};
```

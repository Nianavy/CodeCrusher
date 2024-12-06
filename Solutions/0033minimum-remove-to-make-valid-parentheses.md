https://leetcode.cn/problems/minimum-remove-to-make-valid-parentheses

### Think
```txt
计数括号：

首先，代码通过 count(begin(s), end(s), ')') 计算字符串中右括号 ')' 的数量，并将其存储在 right 变量中。
left 变量用于跟踪左括号 '(' 的数量。
遍历字符串：

使用 for 循环遍历字符串中的每个字符 c。
对于每个字符，进行以下判断：
左括号 '('：
如果 right 大于 0，表示还有未匹配的右括号，可以添加这个左括号到结果字符串 ans 中，同时增加 left 的计数，并减少 right 的计数。
右括号 ')'：
如果 left 大于 0，表示有未匹配的左括号，可以添加这个右括号到结果字符串 ans 中，同时减少 left 的计数。
如果 left 为 0，表示没有可匹配的左括号，直接减少 right 的计数。
其他字符：
直接将字符添加到结果字符串 ans 中。
返回结果：

最后，返回构建好的有效字符串 ans。
```

### Ways
By C++:
```cpp
class Solution {
public:
    string minRemoveToMakeValid(string s) {
        int left = 0;
        int right = count(begin(s), end(s), ')');
        string ans = "";
        for (auto& c : s) {
            if (c == '(') {
                if (right > 0) {
                    ans += c;
                    left++;
                    right--;
                }
            } else if (c == ')') {
                if (left > 0) {
                    ans += c;
                    left--;
                } else {
                    right--;
                }
            } else {
                ans += c;
            }
        }
        return ans;
    }
};
```

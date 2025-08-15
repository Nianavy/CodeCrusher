https://leetcode.cn/problems/valid-parentheses

### Think
```txt
长度检查：

首先检查字符串的长度。如果长度是奇数，直接返回false，因为不可能有成对的括号。
使用哈希表：

使用一个哈希表（unordered_map）来存储每种右括号对应的左括号。这使得在检查时可以快速查找。
使用栈：

使用栈（stack）来存储左括号。当遇到左括号时，将其压入栈中。
当遇到右括号时，检查栈是否为空：
如果栈为空，说明没有对应的左括号，返回false。
如果栈不为空，检查栈顶元素是否与当前右括号匹配。如果匹配，弹出栈顶元素；如果不匹配，返回false。
最终检查：

遍历完字符串后，检查栈是否为空。如果栈为空，说明所有的左括号都有对应的右括号，返回true；如果栈不为空，返回false。
```

### Ways
By C++:
```c++
class Solution {
public:
    bool isValid(string s) {
        int n = s.size();
        if (n % 2 == 1) return false;

        unordered_map<char, char> pairs = {
            {')', '('},
            {']', '['},
            {'}', '{'}
        };

        stack<char> stk;
        for (char c : s) {
            if (pairs.count(c)) {
                if (stk.empty() || stk.top() != pairs[c]) return false;
                stk.pop();
            }
            else stk.push(c);
        }
        return stk.empty();
    }
};
```

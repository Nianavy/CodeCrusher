https://leetcode.cn/problems/basic-calculator

### Think
```txt
使用了一个栈 ops 来存储当前的符号状态。栈的顶部元素表示当前的符号（正或负），在遇到括号时会将当前符号压入栈中。
sign：当前符号，初始为 1（表示正号）。
ret：最终结果，初始为 0。
n：输入字符串的长度。
i：当前遍历的索引。

遍历字符串 s，根据字符的不同类型进行处理：
空格：直接跳过。
加号 +：将当前符号设为栈顶元素（正号）。
减号 -：将当前符号设为栈顶元素的负值（负号）。
左括号 (：将当前符号压入栈中，表示进入一个新的表达式。
右括号 )：弹出栈顶元素，表示结束当前表达式。
数字：通过循环读取数字字符，构建完整的数字，并根据当前符号更新结果 ret。

当遇到数字时，使用一个 while 循环来处理多位数，确保将所有连续的数字字符组合成一个完整的整数。
```

### Ways
By C++:
```cpp
class Solution {
public:
    int calculate(string s) {
        stack<int> ops;
        ops.push(1);
        int sign = 1;
        int ret = 0;
        int n = s.length();
        int i = 0;
        while (i<n) {
            if (s[i] == ' ') ++i;
            else if (s[i] == '+') {
                sign = ops.top();
                ++i;
            }
            else if (s[i] == '-') {
                sign = -ops.top();
                ++i;
            }
            else if (s[i] == '(') {
                ops.push(sign);
                ++i;
            }
            else if (s[i] == ')') {
                ops.pop();
                ++i;
            }
            else {
                long num = 0;
                while (i<n && s[i]>='0' && s[i]<='9') {
                    num = num*10 + s[i] - '0';
                    ++i;
                }
                ret += sign * num;
            }
        }
        return ret;
    }
};
```

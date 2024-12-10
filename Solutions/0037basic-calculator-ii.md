https://leetcode.cn/problems/basic-calculator-ii

### Think
```txt
数据结构：

使用一个 vector<int> stk 来存储计算过程中产生的数字。这个栈的作用是保存当前的计算结果，以便在遇到加法或减法时进行累加。
变量定义：

char preSign = '+';：用于记录前一个操作符，初始为加号。
int num = 0;：用于存储当前读取的数字。
int n = s.length();：获取输入字符串的长度。
遍历字符串：

使用 for 循环遍历字符串 s 的每一个字符。
如果当前字符是数字（isdigit(s[i])），则将其转换为整数并累加到 num 中。
处理操作符：

当遇到非数字字符（且不是空格）时，或者到达字符串末尾（i == n-1），就会根据 preSign 的值来决定如何处理 num：
加法：将 num 压入栈中。
减法：将 -num 压入栈中。
乘法：将栈顶元素与 num 相乘，更新栈顶元素。
除法：将栈顶元素与 num 相除，更新栈顶元素。
更新状态：

更新 preSign 为当前字符，重置 num 为 0，以便处理下一个数字。
返回结果：

最后，使用 accumulate 函数将栈中的所有元素相加，返回最终结果。
```

### Ways
By C++:
```cpp
class Solution {
public:
    int calculate(string s) {
        vector<int> stk;
        char preSign = '+';
        int num = 0;
        int n = s.length();
        for (int i=0; i<n; ++i) {
            if (isdigit(s[i])) {
                num = num * 10 + int(s[i]-'0');
            }
            if (!isdigit(s[i]) && s[i] != ' ' || i == n-1) {
                switch (preSign) {
                    case '+':
                        stk.push_back(num);
                        break;
                    case '-':
                        stk.push_back(-num);
                        break;
                    case '*':
                        stk.back() *= num;
                        break;
                    default:
                        stk.back() /= num;
                }
                preSign = s[i];
                num = 0;
            }
        }
        return accumulate(stk.begin(), stk.end(), 0);
    }
};
```

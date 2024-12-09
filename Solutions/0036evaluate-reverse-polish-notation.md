https://leetcode.cn/problems/evaluate-reverse-polish-notation

### Think
```txt
数据结构选择：

使用了一个栈（stack<int> stk）来存储操作数。栈的后进先出（LIFO）特性非常适合处理逆波兰表达式，因为在遇到操作符时，我们需要先取出最近的两个操作数进行计算。
遍历输入：

通过一个循环遍历输入的字符串数组 tokens。每个元素可以是一个数字或一个操作符。
判断操作数与操作符：

使用 isNumber 函数判断当前的 token 是否为数字。如果是数字，则将其转换为整数并压入栈中。
如果是操作符，则从栈中弹出两个数字（num2 和 num1），并根据操作符进行相应的计算。
执行计算：

使用 switch 语句根据操作符的类型执行加、减、乘、除运算，并将结果压回栈中。
返回结果：

最后，栈顶的元素即为逆波兰表达式的计算结果。
```

### Ways
By C++:
```cpp
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> stk;
        int n = tokens.size();
        for (int i=0; i<n; ++i) {
            string &token = tokens[i];
            if (isNumber(token)) {
                stk.push(atoi(token.c_str()));
            }
            else {
                int num2 = stk.top();
                stk.pop();
                int num1 = stk.top();
                stk.pop();
                switch (token[0]) {
                    case '+':
                        stk.push(num1 + num2);
                        break;
                    case '-':
                        stk.push(num1 - num2);
                        break;
                    case '*':
                        stk.push(num1 * num2);
                        break;
                    case '/':
                        stk.push(num1/num2);
                        break;
                }
            }
        }
        return stk.top();
    }
    bool isNumber(string &token) {
        return !(token == "+" || token == "-" || token == "*" || token == "/");
    }
};
```

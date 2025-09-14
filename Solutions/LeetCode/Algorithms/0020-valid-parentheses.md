# Question

[valid-parentheses](https://leetcode.cn/problems/valid-parentheses/)



# Answer

> solution

解决符号匹配问题（如括号匹配）的核心思路主要围绕着使用栈数据结构来追踪开符号，并在遇到闭符号时进行配对检查。以下是具体的步骤和逻辑：

### 核心思路

1. **初始化一个空栈**：栈用于存储尚未匹配的开符号（'(', '[', '{'等）。栈的特点是后进先出，这使得它非常适合用来处理这种需要配对最近未匹配开符号的问题。

2. **遍历字符串中的每个字符**：
    - 如果当前字符是一个开符号（例如'(', '[', '{'），则将其压入栈中。这意味着我们暂时还不知道这个符号是否正确配对，所以把它保存起来。
    - 如果当前字符是一个闭符号（例如')', ']', '}'），我们需要检查栈顶元素是否为对应的开符号。如果栈为空或者栈顶元素不是对应的开符号，则说明这个闭符号没有匹配的开符号，返回`false`表示匹配失败。否则，从栈中弹出栈顶元素，表示找到了一对匹配的符号。

3. **最终检查**：当字符串中的所有字符都被处理之后，检查栈是否为空。如果栈为空，说明所有的开符号都有正确的闭符号与之匹配，返回`true`；如果栈不为空，意味着存在未匹配的开符号，返回`false`。

### 例子分析

以第一个实现为例：

- 使用数组模拟栈，通过变量`n`记录栈顶位置。
- 遍历输入字符串，对于每个开符号将其加入栈中，对于每个闭符号检查栈顶是否有匹配的开符号。
- 如果遍历结束后栈为空，说明所有符号都正确配对，函数返回`true`。

第二个实现使用了C++ STL中的`std::stack`和`std::unordered_map`来简化操作，但核心逻辑不变。

这种方法不仅适用于圆括号、方括号和花括号的匹配问题，还可以扩展到其他需要配对符号的情况，比如HTML标签的匹配等。关键在于如何利用栈的特性来高效地解决问题。

### Way Of C

> source code

```c
bool isValid(char* s) {
    int n = 0;
    char stack[10000];

    while (*s != '\0') {
        switch (*s) {
            case '(':
            case '[':
            case '{':
                stack[n++] = *s;
                break;
            case ')':
                if (n == 0 || stack[--n] != '(') return false;
                break;
            case ']':
                if (n == 0 || stack[--n] != '[') return false;
                break;
            case '}':
                if (n == 0 || stack[--n] != '{') return false;
                break;
            default:
                return false;
                break;
        }
        ++s;
    }
    return n == 0;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    bool isValid(string s) {
        stack<char> stk;
        unordered_map<char, char> pairs = {
            {')', '('},
            {']', '['},
            {'}', '{'}
        };

        for (char c : s) {
            if (pairs.count(c)) {
                if (stk.empty() || stk.top() != pairs[c]) return false;
                stk.pop();
            } else {
                stk.push(c);
            }
        }
        return stk.empty();
    }
};
```

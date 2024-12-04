https://leetcode.cn/problems/min-stack

### Think
```txt
引入一个辅助栈来跟踪最小值,使得在常数时间内获取栈中的最小元素成为可能,同时不影响基本栈操作的性能。
```

### Ways
By C++:
```cpp
class MinStack {
    stack<int> x_stack;
    stack<int> min_stack;
public:
    MinStack() {
        min_stack.push(INT_MAX);
    }
    
    void push(int x) {
        x_stack.push(x);
        min_stack.push(min(min_stack.top(), x));
    }
    
    void pop() {
        x_stack.pop();
        min_stack.pop();
    }
    
    int top() {
        return x_stack.top();
    }
    
    int getMin() {
        return min_stack.top();
    }
};
```
